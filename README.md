


#🔌 RMshebei Tuya Smart WiFi Breaker 4P100A
Integração com Home Assistant + Cálculo Solar + Dashboard Completo

📺 Vídeo explicando todo o sistema:
<p align="left">
  <a href="https://youtu.be/I6ezCRUxces">
    <img src="https://img.youtube.com/vi/I6ezCRUxces/0.jpg" width="400"/>
  </a>
</p>

---

Projeto completo de monitoramento de energia solar utilizando:

Disjuntor Tuya WiFi 4P100A com medição bidirecional

Sonoff POWR2

Utility Meter do Home Assistant

Cards personalizados (Mushroom + Mod Card + Browser Mod)

Cálculo automático de consumo, produção, injeção e saldo energético


- **⚙️ Equipamentos Utilizados**
  - RMshebei Tuya Smart WiFi Disjuntor 4P100A
  - Sonoff POWR2

⚙️ Integrações

🔹 Home Asitant

🔹 Browser - Mod Add-on 

🔹 Mushroom Cards - Hack

🔹 Card Mod - Hacks

📊 O que esse projeto calcula automaticamente

✔ Produção solar diária

✔ Produção solar mensal

✔ Produção anual

✔ Energia injetada na rede

✔ Consumo da casa

✔ Cálculo do valor da conta

✔ Saldo mensal em kWh

✔ Balanço energético acumulado

Tudo com ciclo mensal ajustado para fechamento no dia 20.

📁 1️⃣ sensors.yaml


📁  Valor  de produção
```yaml
- platform: template
  sensors:
  - platform: template
    sensors:

      producao_solar_semanal:
        friendly_name: "Producao Solar Semanal"
        unit_of_measurement: "kWh"
        value_template: >
          {{ states('sensor.sonoff_1000916c58_energy') | float }}

      producao_solar_anual:
        friendly_name: "Producao Solar Anual"
        unit_of_measurement: "kWh"
        value_template: >
          {{ states('sensor.producao_solar_mensal') | float }}

      energia_injetada_semanal:
        friendly_name: "Energia Injetada Semanal"
        unit_of_measurement: "kWh"
        value_template: >
          {{ states('sensor.breaker_total_production_2') | float }}

      energia_injetada_anual:
        friendly_name: "Energia Injetada Anual"
        unit_of_measurement: "kWh"
        value_template: >
          {{ states('sensor.injecao_energia_mensal') | float }}

      consumo_energia_anual:
        friendly_name: "Consumo Energia Anual"
        unit_of_measurement: "kWh"
        value_template: >
          {{ states('sensor.consumo_energia_mensal') | float }}

      consumo_energia_semanal:
        friendly_name: "Consumo Energia Semanal"
        unit_of_measurement: "kWh"
        value_template: >
          {{ states('sensor.consumo_energia_mensal') | float }}        

# Medidas duas fases em kW
      consumo_atual_rede_kw:
        friendly_name: "Consumo Atual da Rede (kW)"
        unit_of_measurement: "kW"
        value_template: >
          {{
            states('sensor.breaker_potencia_da_fase_a_2') | float(0)
            +
            states('sensor.breaker_potencia_da_fase_b_2') | float(0)
          }}

# Medidas duas fases em W
      consumo_atual_rede_w:
        friendly_name: "Consumo Atual da Rede (W)"
        unit_of_measurement: "W"
        value_template: >
          {{
            (
              states('sensor.breaker_potencia_da_fase_a_2') | float(0)
              +
              states('sensor.breaker_potencia_da_fase_b_2') | float(0)
            ) * 1000
            | round(0)
          }}
          
(Deixei somente o que realmente é necessário. Os outros cálculos já são feitos pelo utility_meter.)
```
📁  Valor do KW
```yaml
      custo_producao_semanal:
        friendly_name: "Custo Produção Semanal"
        
        value_template: >
          {{ (states('sensor.producao_solar_semanal') | float * 1.11) | round(2) }}

      custo_producao_mensal:
        friendly_name: "Custo Produção Mensal"
        
        value_template: >
          {{ (states('sensor.producao_solar_mensal') | float * 1.11) | round(2) }}

      custo_producao_anual:
        friendly_name: "Custo Produção Anual"
        
        value_template: >
          {{ (states('sensor.producao_solar_anual') | float * 1.11) | round(2) }}

      custo_injetado_semanal:
        friendly_name: "Custo Injetado Semanal"
        
        value_template: >
          {{ (states('sensor.energia_injetada_semanal') | float * 1.11) | round(2) }}

      custo_injetado_mensal:
        friendly_name: "Custo Injetado Mensal"
        
        value_template: >
          {{ (states('sensor.injecao_energia_mensal') | float * 1.11) | round(2) }}

      custo_injetado_anual:
        friendly_name: "Custo Injetado Anual"
        
        value_template: >
          {{ (states('sensor.energia_injetada_anual') | float * 1.11) | round(2) }}
          
      custo_consumo_semanal:
        friendly_name: "Custo Consumo Semanal"
        
        value_template: >
          {{ (states('sensor.breaker_energia_total_2') | float * 1.11) | round(2) }}

      custo_consumo_anual:
        friendly_name: "Custo Consumo Anual"
        
        value_template: >
          {{ (states('sensor.consumo_energia_anual') | float * 1.11) | round(2) }}

      custo_potencia_atual:
        friendly_name: "Custo Potência Atual"
        
        value_template: >
          {{ ((states('sensor.sonoff_1000916c58_power') | float / 1000) * 1.11) | round(2) }}

      custo_energia_injetada:
        friendly_name: "Custo Energia Injetada"
        
        value_template: >
          {{ (states('sensor.breaker_total_production_2') | float * 1.11) | round(2) }}

      custo_consumo_total:
        friendly_name: "Custo Energia Consumida Total"
        
        value_template: >
          {{ (states('sensor.breaker_energia_total_2') | float * 1.11) | round(2) }}

      custo_consumo_dia:
        friendly_name: "Custo Consumo Hoje"
        
        value_template: >
          {{ (states('sensor.consumo_energia_diario') | float * 1.11) | round(2) }}

      custo_consumo_mensal:
        friendly_name: "Custo Consumo Mensal"
        
        value_template: >
          {{ (states('sensor.consumo_energia_mensal') | float * 1.11) | round(2) }}          
 
      custo_energia_kwh:
        friendly_name: "Valor kWh"
        unit_of_measurement: "R$/kWh"
        value_template: "1.11"

      custo_energia_dia:
        friendly_name: "Custo Energia Dia"
        unit_of_measurement: "R$"
        value_template: >
          {{ (states('sensor.consumo_energia_diario') | float * 1.11) | round(2) }}

      custo_energia_mes:
        friendly_name: "Custo Energia Mês"
        unit_of_measurement: "R$"
        value_template: >
          {{ (states('sensor.consumo_energia_mensal') | float * 1.11) | round(2) }}
                
      valor_conta_mensal:
        friendly_name: "Valor da Conta Atual"
        
        value_template: >
          {{ (states('sensor.consumo_mensal_fatura') | float * 1.11) | round(2) }}         
          
```

📁  Balanço Total 
```yaml

      balanco_energetico_acumulado:
        friendly_name: "Balanço Energético Acumulado"
        unit_of_measurement: "kWh"
        value_template: >
          {{ (states('sensor.breaker_energia_total_2') | float(0)
           - states('sensor.breaker_total_production_2') | float(0)) | round(2) }} 
           
      saldo_mensal_kwh:
        friendly_name: "Saldo da Conta (kWh)"
        unit_of_measurement: "kWh"
        value_template: >
          {{ (states('sensor.producao_solar_mensal') | float(0)
           - states('sensor.consumo_energia_mensal') | float(0)) | round(2) }}         
(Deixei somente o que realmente é necessário. Os outros cálculos já são feitos pelo utility_meter.)
```
📁 2️⃣ utility_meter.yaml

```yaml
consumo_energia_mensal:
  source: sensor.breaker_energia_total_2
  cycle: monthly
  offset:
    days: 20

injecao_energia_mensal:
  source: sensor.breaker_total_production_2
  cycle: monthly
  offset:
    days: 20

producao_solar_mensal:
  source: sensor.sonoff_1000916c58_energy
  cycle: monthly
  offset:
    days: 20

consumo_energia_anual:
  source: sensor.breaker_energia_total_2
  cycle: yearly

energia_injetada_anual:
  source: sensor.breaker_total_production_2
  cycle: yearly

producao_solar_anual:
  source: sensor.sonoff_1000916c58_energy
  cycle: yearly

Agora está limpo, organizado e sem duplicação desnecessária.
```



---
📸 Preview da Dashboard

<!-- Imagem principal -->
<img src="https://raw.githubusercontent.com/viniciusmbs/Disjuntor-4P100A-/main/Captura%20de%20tela%202026-02-24%20173248.png" 
alt="Dashboard Principal" 
width="700"/>

📁 3️⃣ Card com Popup (cards/card_popup.yaml)

```yaml
type: custom:mushroom-template-card
primary: >
  Produção Solar: {{ states("sensor.sonoff_1000916c58_power") | float(0) | round(2) }} W
secondary: >
  Dia: {{ states("sensor.sonoff_1000916c58_energy") | float(0) | round(2) }} kWh
  | Mês: {{ states("sensor.producao_solar_mensal") | float(0) | round(2) }} kWh
icon: mdi:solar-panel
icon_color: green
tap_action:
  action: fire-dom-event
  browser_mod:
    service: browser_mod.popup
    data:
      content:
        !include card_simples.yaml
```
---
<!-- Galeria de miniaturas -->
<table>
  <tr>
   <td>
  <img src="https://raw.githubusercontent.com/viniciusmbs/Disjuntor-4P100A-/main/Captura%20de%20tela%202026-02-24%20173304.png" 
  alt="Tela 1" 
  width="150"/>
</td>
  </tr>
</table>

---
📁 4️⃣ Card Simples (cards/card_simples.yaml)

```yaml
type: custom:mod-card
style: |
  ha-card {
    background: linear-gradient(
      rgba(0,0,0,0.65),
      rgba(0,0,0,0.65)
    ), url("/local/cardpainel2.jpg") center/cover no-repeat;
    border-radius: 18px;
    padding: 16px;
    box-shadow: 0 8px 30px rgba(0,0,0,0.4);
  }
card:
  type: vertical-stack
  cards:
    - type: entities
      title: Produção Solar
      entities:
        - sensor.sonoff_1000916c58_power
        - sensor.producao_solar_mensal
        - sensor.producao_solar_anual

        
