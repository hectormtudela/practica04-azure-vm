# Práctica 04 — Desplegar un recurso real y medir su coste en Azure

**Alumno:** hectormtudela06
**Resource Group:** `rg-practica04-vm-hectormtudela`
**Fecha de inicio:** 22/09/2026
**Región final de la VM:** Spain Central *(ver Sección 6 — no fue una elección libre, sino el resultado de varias restricciones reales de la suscripción)*

---

## Sección 3 — Estimación de coste (Pricing Calculator)

![Pricing Calculator - VM](screenshots/dia1/01a-pricing-vm.png)
![Pricing Calculator - Disco](screenshots/dia1/01b-pricing-disco.png)
![Pricing Calculator - IP y total](screenshots/dia1/01c-pricing-ip-total.png)

| Escenario | Horas de cómputo | Coste cómputo | Coste disco (72 h) | Coste IP (72 h) | Total estimado |
|---|---|---|---|---|---|
| A. VM encendida todo el laboratorio | 72 | 2,95 € | 0,22 € | 0,31 € | **3,48 €** |
| B. VM con apagado automático nocturno (~12 h/día) | 36 | 1,48 € | 0,22 € | 0,31 € | **2,01 €** |
| C. VM encendida solo 8 h en total | 8 | 0,33 € | 0,22 € | 0,31 € | **0,86 €** |

**Datos base (Austria East, en euros):**
- VM B2s: 0,041 €/hora
- Disco Standard SSD 32GB: 2,23 €/mes → 0,22 € a 72h
- IP pública Standard estática: 3,13 €/mes → 0,31 € a 72h

---

## Sección 4 — Resource Group etiquetado

![Resource Group con Tags](screenshots/dia1/02-resource-group-tags.png)

| Tag | Valor |
|---|---|
| Project | Practica04 |
| Department | Data |
| Environment | Lab |
| Owner | hectormtudela06 |
| CostCenter | DataNova-Analytics |

---

## Sección 5 — Budget del laboratorio

![Configuración del Budget](screenshots/dia1/03-budget-config.png)
![Alertas del Budget](screenshots/dia1/04-budget-alertas.png)

| Campo | Valor |
|---|---|
| Name | `Budget-Practica04-VM-hectormtudela` |
| Amount | 3 € (Escenario B: 2,01 € redondeado) |
| Reset period | Monthly |

| Tipo | Umbral | Importe | Qué significa |
|---|---|---|---|
| Actual | 50 % | 1,5 € | Se ha gastado la mitad de lo previsto |
| Actual | 80 % | 2,4 € | El laboratorio se acerca al límite |
| Actual | 100 % | 3 € | Se ha alcanzado lo estimado |
| Forecasted | 100 % | 3 € | Azure prevé que el mes superará el Budget |

**Predicción (antes del Día 2):** _(escribe aquí si crees que se activará la alerta Forecasted)_

---

## Sección 6 — Despliegue de la máquina virtual

### 6.1 — Configuración final

| Dato | Valor |
|---|---|
| Región final | **Spain Central** |
| Tamaño final | **Standard_B2s_v2** (2 vCPU, 8 GiB RAM) — sustituto de `B2s`, ver 6.2 |
| Precio por hora (estimado, B2s en Austria East) | 0,041 €/hora |
| Precio real (B2s_v2, Pricing Calculator en Spain Central) | *(pendiente — consultar calculador)* |
| Fecha y hora de creación | 22/09/2026, 20:38 UTC |
| Método de despliegue | **Azure CLI (Cloud Shell)**, tras fallos repetidos del asistente gráfico del portal |
| Apagado automático | No configurado — indicación del profesor por fallo conocido de la plataforma en esta práctica |
| Disco del SO | Standard SSD, eliminar con VM ✅ |
| Puertos de entrada | Ninguno |

### 6.2 — Diario de incidencias (importante para el informe)

Esta sección documenta por qué la configuración final difiere de la especificada originalmente por la práctica (`Standard_B2s` en una región europea cualquiera), y sirve como evidencia de resolución de problemas reales de plataforma:

1. **Región `Austria East` no aparece en el selector del asistente** → sustituida por `West Europe`.
2. **`West Europe` bloqueada por política de la suscripción** (error `RequestDisallowedByAzure` en "Revisar y crear", afectando a todos los recursos: VM, disco, IP, NIC, NSG, VNet).
3. **Consulta a Azure Policy → Assignments → "Allowed resource deployment regions"**: la suscripción `Azure for Students` solo permite desplegar en 5 regiones exactas: `germanywestcentral`, `belgiumcentral`, `spaincentral`, `italynorth`, `francecentral`. Estas no coinciden con las que el asistente gráfico ofrece como "Recomendado", lo que causó varios intentos fallidos previos.
4. **`France Central` sí está permitida, pero el tamaño `B2s` (y toda la serie B) aparece como "Tamaño no disponible"** ahí.
5. **Consulta a Suscripción → Uso y cuotas**, filtrando por `Microsoft.Compute` y familia `BS`: se confirma que la familia **`Bsv2`** tiene cuota disponible (10 vCPUs, 0% de uso) en **Spain Central**, mientras que otras combinaciones región/familia devuelven error al consultarlas.
6. **Se despliega la VM con Azure CLI** (`az vm create`) en `spaincentral` con `Standard_B2s`: falla con **`SkuNotAvailable` por restricción de capacidad** (no de política — Azure confirma que la región está permitida, pero no hay capacidad física de ese tamaño en ese momento).
7. **Se repite el comando sustituyendo el tamaño por `Standard_B2s_v2`** (misma familia B, 2 vCPU, más RAM): **despliegue correcto**, confirmado en el Registro de actividad del Resource Group (`Create or Update Virtual Machine — Correcto`).
8. **Apagado automático por CLI (`az vm auto-shutdown`) también falla** con el mismo error de política (`RequestDisallowedByAzure`), porque ese comando usa por defecto la región del *Resource Group* (creado originalmente en Austria East) en vez de la región real de la VM. El profesor indica que esta función tiene un fallo conocido en la práctica y que no es necesario configurarla.

**Conclusión:** la sustitución de `B2s` por `B2s_v2` y el cambio de región de Austria East a Spain Central están justificados por restricciones reales, verificables y documentadas de la suscripción — no por elección arbitraria. Esta diferencia se recoge en la comparación estimado vs. real de la Sección 10.

### 6.3 — Capturas a guardar en `screenshots/dia1/`

| # | Nombre de archivo sugerido | Qué debe mostrar |
|---|---|---|
| 1 | `06a-wizard-basics-warning-tamano.png` | Aviso inicial de tamaño no disponible en el asistente clásico (Datos básicos) |
| 2 | `06b-wizard-size-picker-westeurope.png` | Selector de tamaños en West Europe mostrando B2s bloqueado y B2s_v2 disponible |
| 3 | `06c-review-create-errores-westeurope.png` | Pantalla de "Revisar y crear" con los errores `RequestDisallowedByAzure` en West Europe |
| 4 | `06d-policy-allowed-regions.png` | Azure Policy → Assignments → "Allowed resource deployment regions" con la lista de 5 regiones permitidas |
| 5 | `06e-quotas-bs-family.png` | Suscripción → Uso y cuotas, filtrado por familia BS/Bsv2, mostrando cuota 10 en Spain Central |
| 6 | `06f-cli-error-skunotavailable.png` (o `.txt`) | Salida de terminal del error `SkuNotAvailable` al crear con B2s en Spain Central |
| 7 | `06g-vm-creada-overview.png` | Página de la VM ya creada: Spain Central, Standard B2s v2, En ejecución, IP pública, tags |
| 8 | `06h-activity-log.png` | Registro de actividad del Resource Group mostrando "Create Deployment — Error" seguido de "Create or Update Virtual Machine — Correcto" |

*(Las capturas de la pestaña Redes, Administración y Etiquetas del asistente que ya hiciste durante el proceso también puedes incluirlas como `06i`, `06j`, `06k` si quieres más detalle, aunque no son imprescindibles ya que el resultado final se ve en la #7.)*

---

## Sección 7 — Verificación del etiquetado

| Recurso | Tipo | ¿Tiene los 5 Tags? | ¿Genera coste? |
|---|---|---|---|
| vm-practica04-hectormtudela | Virtual machine | | |
| | Disk | | |
| | Public IP address | | |
| | Network interface | | |
| | Network security group | | |
| | Virtual network | | |

---

## Sección 8 — Primera revisión en Cost Analysis (Día 2)

**Primera comprobación (23/09/2026, mañana):** Coste real acumulado = **0,01 €**. Previsión ("Forecast") todavía no disponible por falta de histórico suficiente — es normal, según la práctica los datos de coste tardan entre 8 y 24 horas en consolidarse. Se repetirá la comprobación más avanzado el día.

![Cost Analysis - Resources](screenshots/dia2/10-cost-analysis-resources.png)

| Recurso | Coste acumulado |
|---|---|
| Máquina virtual | |
| Disco | |
| IP pública | |
| Otros | |
| **Total (Actual Cost)** | |

![Cost Analysis - Meter](screenshots/dia2/11-cost-analysis-meter.png)
![Cost Analysis - Daily](screenshots/dia2/12-cost-analysis-daily.png)
![Cost Analysis - Tags](screenshots/dia2/13-cost-analysis-tags.png)
![Forecast](screenshots/dia2/14-forecast.png)

| Dato | Valor |
|---|---|
| Actual Cost | |
| Forecasted Cost (fin de mes) | |
| Budget del laboratorio | |
| ¿Actual supera el Budget? | |
| ¿Forecast supera el Budget? | |

![Alertas recibidas](screenshots/dia2/15-alertas-recibidas.png)

| Alerta | ¿Se ha activado? | Fecha y hora |
|---|---|---|
| Actual 50 % | | |
| Actual 80 % | | |
| Actual 100 % | | |
| Forecasted 100 % | | |

**¿Se cumplió la predicción de la Sección 5?** _(respuesta)_

---

## Sección 9 — Experimento: apagar vs. desasignar

![VM deallocate](screenshots/dia2/16-vm-deallocate.png)

| Medidor | Coste Día 1 | Coste Día 2 | Coste Día 3 |
|---|---|---|---|
| Cómputo B2s | | | |
| Disco Standard SSD | | | |
| IP pública | | | |

1. ¿Qué medidor baja cuando la VM está desasignada? _(respuesta)_
2. ¿Qué medidores siguen generando coste? _(respuesta)_
3. Si un compañero deja 20 VMs apagadas desde el sistema operativo durante un fin de semana, ¿qué está pagando DataNova? _(respuesta)_
4. ¿Qué acción garantizaría que ningún medidor siga cobrando? _(respuesta)_

---

## Sección 10 — Comparación estimación vs. real (Día 3)

![Comparación diaria](screenshots/dia3/17-cost-analysis-comparacion-diaria.png)
![Comparación estimado vs real](screenshots/dia3/18-comparacion-estimado-real.png)

| Concepto | Estimado (Sección 3) | Real (Cost Analysis) | Diferencia |
|---|---|---|---|
| Cómputo | | | |
| Disco | | | |
| IP pública | | | |
| **Total** | | | |

1. ¿Qué escenario de la Sección 3 se parece más a lo que realmente ocurrió? _(respuesta)_
2. ¿Qué componente se desvió más de lo estimado? ¿Por qué? _(respuesta)_
3. ¿Algún coste no estaba en tu estimación? _(respuesta)_
4. Coste estimado para 10 analistas, 8h/día, 22 días, con los datos reales: _(respuesta)_

---

## Sección 11 — Limpieza

![Resource Group delete](screenshots/dia3/19-resource-group-delete.png)
![Verificación coste cero](screenshots/dia3/20-verificacion-coste-cero.png)

- [ ] Resource group eliminado
- [ ] Verificado que no queda nada bajo el Tag `Project = Practica04`
- [ ] Budget del laboratorio eliminado
- [ ] Coste diario en 0 € 24h después (verificación final)

---

## Sección 12 — Informe final

| Elemento | Resultado |
|---|---|
| Nombre de la práctica | Práctica 04 — VM Linux con control de costes |
| Resource Group | rg-practica04-vm-hectormtudela |
| Servicios utilizados | |
| Coste estimado antes de desplegar | |
| Budget disponible | |
| Alertas configuradas | |
| Alertas activadas | |
| Coste observado | |
| Recurso con mayor coste | |
| Recursos eliminados | |
| Observaciones | |

---

## Sección 13 — Ejercicio final (caso DataNova Engineering)

**Datos:**
- Budget del proyecto = 50 €
- Actual Cost (día 10) = 22 €
- Forecasted Cost = 68 €
- Apagado automático = No configurado
- Estado de 3 VMs = Stopped (no deallocated)

1. ¿Qué alertas se habrán activado? _(respuesta)_
2. ¿Por qué el Forecasted Cost es tan superior al Actual Cost? _(respuesta)_
3. ¿Qué parte del gasto de las 3 VMs detenidas se podría eliminar sin borrarlas? _(respuesta)_
4. Ordena de mayor a menor impacto en ahorro inmediato: _(respuesta)_
5. ¿Qué acción no ahorra dinero pero es imprescindible para analizarlo? _(respuesta)_
