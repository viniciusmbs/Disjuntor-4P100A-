

## 🔌 RMshebei Tuya Smart WiFi Breaker 4P100A
Integração com Home Assistant + Cálculo Solar + Dashboard Completo

📺 Vídeo explicando todo o sistema:
<p align="left">
  <a href="https://youtu.be/I6ezCRUxces">
   <img src="https://raw.githubusercontent.com/viniciusmbs/Disjuntor-4P100A-/main/CAPA.png" width="400"/>
  </a>
</p>

---

## ☀️ Projeto de Monitoramento de Energia Solar

Sistema completo de monitoramento de energia solar utilizando:

Disjuntor WiFi 4P100A com medição bidirecional

Medidor inteligente dedicado para produção solar

Utility Meter do Home Assistant

Cards personalizados (Mushroom + Card Mod + Browser Mod)

Automação própria para reset do ciclo de faturamento

O projeto realiza o cálculo automático de:

Consumo

Produção

Energia injetada

Saldo energético

Valor estimado da conta

Sem depender de offset automático do Utility Meter.

⚙️ Equipamentos Utilizados

RMshebei Tuya Smart WiFi Disjuntor 4P100A

Sonoff POWR2

🔌 Integrações Utilizadas

🔹 Home Assistant

🔹 Browser Mod

🔹 Mushroom Cards

🔹 Card Mod

📊 O que o projeto calcula automaticamente

✔ Produção solar diária
✔ Produção solar mensal
✔ Produção solar anual
✔ Energia injetada na rede
✔ Consumo total da residência
✔ Cálculo automático do valor da conta (baseado no valor do kWh configurado)
✔ Saldo mensal em kWh
✔ Balanço energético acumulado
✔ Crédito ou débito energético do mês

🔄 Fechamento de Ciclo (IMPORTANTE)

Este projeto não utiliza offset no Utility Meter.

O fechamento da fatura é realizado através de:

✔ Automação personalizada
✔ Reset manual controlado
✔ Execução automática no dia definido (ex: dia 25 às 23:59)

Isso permite:

Maior controle sobre o período de faturamento

Independência do ciclo fixo do Utility Meter

Ajuste fácil caso a concessionária altere a data de leitura

🎯 Diferencial do Projeto

✔ Sistema totalmente modular
✔ Não interfere em outros cards que utilizem os mesmos sensores
✔ Reset centralizado e organizado
✔ Fácil adaptação para qualquer valor de kWh
✔ Estrutura pronta para publicação no GitHu

 📁 1️⃣ sensors.yaml


### 📁  Valor  de produção
```yaml
##################################################
# SENSORES (PLATFORM TEMPLATE)
##################################################
- platform: template

sensor:
  - platform: template
    sensors:

      ##################################################
      # PRODUÇÃO SOLAR (BASE)
      ##################################################

      producao_solar_semanal:
        friendly_name: "Producao Solar Semanal"
        unit_of_measurement: "kWh"
        value_template: >
          {{ states('sensor.sonoff_1000916c58_energy') | float(0) }}

      ##################################################
      # CONSUMO ATUAL DUAS FASES
      ##################################################

      consumo_atual_rede_kw:
        friendly_name: "Consumo Atual da Rede (kW)"
        unit_of_measurement: "kW"
        value_template: >
          {{
            states('sensor.breaker_potencia_da_fase_a_2') | float(0)
            +
            states('sensor.breaker_potencia_da_fase_b_2') | float(0)
          }}

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
          }}

      ##################################################
      # CUSTOS (base fixa 1.11)
      ##################################################

      valor_kwh:
        friendly_name: "Valor kWh"
        unit_of_measurement: "R$/kWh"
        value_template: "1.11"

      custo_consumo_mensal:
        friendly_name: "Custo Consumo Mensal"
        unit_of_measurement: "R$"
        value_template: >
          {{ states('sensor.consumo_energia_mensal') | float(0) * 1.11 }}

      custo_producao_mensal:
        friendly_name: "Custo Produção Mensal"
        unit_of_measurement: "R$"
        value_template: >
          {{ states('sensor.producao_solar_mensal') | float(0) * 1.11 }}

      custo_injetado_mensal:
        friendly_name: "Custo Injetado Mensal"
        unit_of_measurement: "R$"
        value_template: >
          {{ states('sensor.injecao_energia_mensal') | float(0) * 1.11 }}

      ##################################################
      # BALANÇOS
      ##################################################

      balanco_energetico_acumulado:
        friendly_name: "Balanço Energético Acumulado"
        unit_of_measurement: "kWh"
        value_template: >
          {{
            states('sensor.breaker_energia_total_2') | float(0)
            -
            states('sensor.breaker_total_production_2') | float(0)
          }}

      saldo_conta_kwh:
        friendly_name: "Saldo da Conta (kWh)"
        unit_of_measurement: "kWh"
        value_template: >
          {{
            states('sensor.producao_solar_mensal') | float(0)
            -
            states('sensor.consumo_energia_mensal') | float(0)
          }}
```
 ###📁  Valor do KW
```yaml
##################################################
# VALOR DO KWH E CÁLCULOS DE CUSTO
##################################################

    custo_producao_semanal:
      friendly_name: "Custo Produção Semanal"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.producao_solar_semanal') | float(0) * 1.11) | round(2) }}

    custo_producao_mensal:
      friendly_name: "Custo Produção Mensal"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.producao_solar_mensal') | float(0) * 1.11) | round(2) }}

    custo_producao_anual:
      friendly_name: "Custo Produção Anual"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.producao_solar_anual') | float(0) * 1.11) | round(2) }}

    custo_injetado_semanal:
      friendly_name: "Custo Injetado Semanal"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.energia_injetada_semanal') | float(0) * 1.11) | round(2) }}

    custo_injetado_mensal:
      friendly_name: "Custo Injetado Mensal"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.injecao_energia_mensal') | float(0) * 1.11) | round(2) }}

    custo_injetado_anual:
      friendly_name: "Custo Injetado Anual"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.energia_injetada_anual') | float(0) * 1.11) | round(2) }}

    custo_consumo_semanal:
      friendly_name: "Custo Consumo Semanal"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.breaker_energia_total_2') | float(0) * 1.11) | round(2) }}

    custo_consumo_anual:
      friendly_name: "Custo Consumo Anual"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.consumo_energia_anual') | float(0) * 1.11) | round(2) }}

    custo_potencia_atual:
      friendly_name: "Custo Potência Atual"
      unit_of_measurement: "R$"
      value_template: >
        {{ ((states('sensor.sonoff_1000916c58_power') | float(0) / 1000) * 1.11) | round(2) }}

    custo_energia_injetada:
      friendly_name: "Custo Energia Injetada"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.breaker_total_production_2') | float(0) * 1.11) | round(2) }}

    custo_consumo_total:
      friendly_name: "Custo Energia Consumida Total"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.breaker_energia_total_2') | float(0) * 1.11) | round(2) }}

    custo_consumo_dia:
      friendly_name: "Custo Consumo Hoje"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.consumo_energia_diario') | float(0) * 1.11) | round(2) }}

    custo_consumo_mensal:
      friendly_name: "Custo Consumo Mensal"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.consumo_energia_mensal') | float(0) * 1.11) | round(2) }}

    custo_energia_kwh:
      friendly_name: "Valor kWh"
      unit_of_measurement: "R$/kWh"
      value_template: "1.11"

    custo_energia_dia:
      friendly_name: "Custo Energia Dia"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.consumo_energia_diario') | float(0) * 1.11) | round(2) }}

    custo_energia_mes:
      friendly_name: "Custo Energia Mês"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.consumo_energia_mensal') | float(0) * 1.11) | round(2) }}

    valor_conta_mensal:
      friendly_name: "Valor da Conta Atual"
      unit_of_measurement: "R$"
      value_template: >
        {{ (states('sensor.consumo_mensal_fatura') | float(0) * 1.11) | round(2) }}
```

###📁  Balanço Total 
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
  Produção Solar: {{ states("sensor.sonoff_1000916c58_power") | float(0) |
  round(2) }} W
secondary: >
  Dia: {{ states("sensor.sonoff_1000916c58_energy") | float(0) | round(2) }} kWh
  | Mês: {{ states("sensor.producao_solar_mensal") | float(0) | round(2) }} kWh
icon: mdi:solar-panel
icon_color: green
badge_icon: mdi:solar-power-variant-outline
badge_color: green
tap_action:
  action: fire-dom-event
  browser_mod:
    service: browser_mod.sequence
    data:
      sequence:
        - service: script.pc_click
        - service: browser_mod.popup
          data:
            content:
              type: custom:mod-card
              style: |
                ha-card {
                  background: linear-gradient(
                    rgba(0,0,0,0.65),
                    rgba(0,0,0,0.65)
                  ), url("/local/cardpainel2.jpg") center/cover no-repeat;
                  background-size: cover;
                  background-position: center;
                  border-radius: 18px;
                  padding: 16px;
                  box-shadow: 0 8px 30px rgba(0,0,0,0.4);
                }
              card:
                type: vertical-stack
                cards:
                  - type: entities
                    title: Produção Solar
                    card_mod:
                      style: |
                        ha-card {
                          background: transparent;
                          box-shadow: none;
                        }
                    entities:
                      - entity: sensor.sonoff_1000916c58_power
                        name: Potência Atual
                        icon: none
                      - entity: sensor.producao_solar_mensal
                        name: Produção no Mês
                        icon: none
                      - entity: sensor.producao_solar_anual
                        name: Produção no Ano
                        icon: none
                  - type: entities
                    title: Consumo da Casa
                    card_mod:
                      style: |
                        ha-card {
                          background: transparent;
                          box-shadow: none;
                        }
                    entities:
                      - entity: sensor.consumo_atual_rede_w
                        name: Consumo Atual
                        icon: none
                      - entity: sensor.breaker_energia_total_2
                        name: Energia Total
                        icon: none
                      - entity: sensor.consumo_energia_mensal
                        name: Consumo Mensal
                        icon: none
                      - entity: sensor.valor_conta_mensal_real
                        name: Valor Atual da Conta
                        icon: none
                  - type: entities
                    title: Energia Injetada
                    card_mod:
                      style: |
                        ha-card {
                          background: transparent;
                          box-shadow: none;
                        }
                    entities:
                      - entity: sensor.injecao_energia_mensal
                        name: Injetado no Mês
                        icon: none
                      - entity: sensor.energia_injetada_anual
                        name: Injetado no Ano
                        icon: none
                      - type: custom:mushroom-template-card
                        entity: sensor.saldo_mensal_kwh
                        primary: Saldo da Conta (Mensal)
                        secondary: >
                          {% set v = states('sensor.saldo_mensal_kwh')|float %}
                          {% if v >= 0 %}
                            + {{ v | round(2) }} kWh
                          {% else %}
                            - {{ (v | abs) | round(2) }} kWh
                          {% endif %}
                        icon: mdi:circle
                        icon_color: |
                          {% if states('sensor.saldo_mensal_kwh')|float >= 0 %}
                            green
                          {% else %}
                            red
                          {% endif %}
                        layout: horizontal
                        fill_container: true

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
    background-size: cover;
    background-position: center;
    border-radius: 18px;
    padding: 16px;
    box-shadow: 0 8px 30px rgba(0,0,0,0.4);
  }
card:
  type: vertical-stack
  cards:
    - type: entities
      title: Produção Solar
      card_mod:
        style: |
          ha-card {
            background: transparent;
            box-shadow: none;
          }
      entities:
        - entity: sensor.sonoff_1000916c58_power
          name: Potência Atual
        - entity: sensor.producao_solar_mensal
          name: Produção no Mês
        - entity: sensor.producao_solar_anual
          name: Produção no Ano
    - type: entities
      title: Consumo da Casa
      card_mod:
        style: |
          ha-card {
            background: transparent;
            box-shadow: none;
          }
      entities:
        - entity: sensor.breaker_energia_total_2
          name: Energia Total
        - entity: sensor.consumo_energia_mensal
          name: Consumo Mensal
        - entity: sensor.valor_conta_mensal_real
          name: Valor Atual da Conta
    - type: entities
      title: Energia Injetada
      card_mod:
        style: |
          ha-card {
            background: transparent;
            box-shadow: none;
          }
      entities:
        - entity: sensor.injecao_energia_mensal
          name: Injetado no Mês
        - entity: sensor.energia_injetada_anual
          name: Injetado no Ano
        - type: custom:mushroom-template-card
          entity: sensor.saldo_mensal_kwh
          primary: Saldo da Conta (Mensal)
          secondary: >
            {% set v = states('sensor.saldo_mensal_kwh')|float %} {% if v >= 0
            %}
              + {{ v | round(2) }} kWh
            {% else %}
              - {{ (v | abs) | round(2) }} kWh
            {% endif %}
          icon: mdi:circle
          icon_color: |
            {% if states('sensor.saldo_mensal_kwh')|float >= 0 %}
              green
            {% else %}
              red
            {% endif %}
          layout: horizontal
          fill_container: true

```

---

📁 5️⃣ Automação de Reset dos Ciclos Energéticos

```yaml

alias: RESET GERAL TOTAL DIA 25
description: Reseta sensores mensal, semanal e diário antes da leitura dia 26
trigger:
  - platform: time
    at: "23:59:00"

condition:
  - condition: template
    value_template: "{{ now().day == 25 }}"

action:

  ##################################################
  # RESET DIÁRIO
  ##################################################

  - service: utility_meter.reset
    target:
      entity_id:
        - sensor.consumo_energia_diario
        - sensor.para_rede_diario
        - sensor.via_rede_diario
        - sensor.solar_diario
        - sensor.tempo_aquecedor_diario

  ##################################################
  # RESET SEMANAL
  ##################################################

  - service: utility_meter.reset
    target:
      entity_id:
        - sensor.producao_solar_semanal
        - sensor.energia_injetada_semanal
        - sensor.consumo_energia_semanal
        - sensor.tempo_aquecedor_semanal

  ##################################################
  # RESET MENSAL (CARD)
  ##################################################

  - service: utility_meter.reset
    target:
      entity_id:
        - sensor.consumo_energia_mensal
        - sensor.injecao_energia_mensal
        - sensor.producao_solar_mensal

  ##################################################
  # RESET INPUTS
  ##################################################

  - service: input_number.set_value
    target:
      entity_id:
        - input_number.tempo_inicial
        - input_number.tempo_final
        - input_number.tempo_total
        - input_number.consumo_boiler
        - input_number.custo_boiler
        - input_number.qtd_ligado
        - input_number.tempo_medio_aquecimento
        - input_number.producao_mensal_hoymiles
    data:
      value: 0

mode: single        

```
Vini©¿©ius Mendes Corporations® 27/02/2026
Sistema avançado de monitoramento energético desenvolvido para automação residencial inteligente com Home Assistant.

---
