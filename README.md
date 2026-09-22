# Práctica 04 — Desplegar un recurso real y medir su coste en Azure

**Alumno:** hectormtudela06
**Región:** West Europe
**Resource Group:** `rg-practica04-vm-hectormtudela`
**Fecha de inicio:** 22/09/2026

---

## Sección 3 — Estimación de coste (Pricing Calculator)

![Pricing Calculator](screenshots/dia1/01-pricing-calculator-escenarios.png)

| Escenario | Horas de cómputo | Coste cómputo | Coste disco (72 h) | Coste IP (72 h) | Total estimado |
|---|---|---|---|---|---|
| A. VM encendida todo el laboratorio | 72 | | | | |
| B. VM con apagado automático nocturno (~12 h/día) | 36 | | | | |
| C. VM encendida solo 8 h en total | 8 | | | | |

---

## Sección 4 — Resource Group etiquetado

![Resource Group con Tags](screenshots/dia1/02-resource-group-tags.png)

| Tag | Valor |
|---|---|
| Project | Practica04 |
| Department | Data |
| Environment | Lab |
| Owner | az01 |
| CostCenter | DataNova-Analytics |

---

## Sección 5 — Budget del laboratorio

![Configuración del Budget](screenshots/dia1/03-budget-config.png)
![Alertas del Budget](screenshots/dia1/04-budget-alertas.png)

| Campo | Valor |
|---|---|
| Name | `Budget-Practica04-VM-az01` |
| Amount | _(Escenario B redondeado)_ |
| Reset period | Monthly |

| Tipo | Umbral | Importe | Qué significa |
|---|---|---|---|
| Actual | 50 % | | Se ha gastado la mitad de lo previsto |
| Actual | 80 % | | El laboratorio se acerca al límite |
| Actual | 100 % | | Se ha alcanzado lo estimado |
| Forecasted | 100 % | | Azure prevé que el mes superará el Budget |

**Predicción (antes del Día 2):** _(escribe aquí si crees que se activará la alerta Forecasted)_

---

## Sección 6 — Despliegue de la máquina virtual

![VM - Basics](screenshots/dia1/05-vm-basics.png)
![VM - Disks](screenshots/dia1/06-vm-disks.png)
![VM - Management (Auto-shutdown)](screenshots/dia1/07-vm-management-autoshutdown.png)
![VM - Tags](screenshots/dia1/08-vm-tags.png)
![VM - Review + create](screenshots/dia1/09-vm-review-create.png)

| Dato | Valor |
|---|---|
| Precio por hora mostrado por el portal | |
| Precio por hora según tu estimación | |
| Fecha y hora de creación | |

---

## Sección 7 — Verificación del etiquetado

| Recurso | Tipo | ¿Tiene los 5 Tags? | ¿Genera coste? |
|---|---|---|---|
| vm-practica04-az01 | Virtual machine | | |
| | Disk | | |
| | Public IP address | | |
| | Network interface | | |
| | Network security group | | |
| | Virtual network | | |

---

## Sección 8 — Primera revisión en Cost Analysis (Día 2)

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
| Resource Group | rg-practica04-vm-az01 |
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