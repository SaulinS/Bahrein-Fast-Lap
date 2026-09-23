# Modelando o Tempo de Volta na Fórmula 1: Regressão Linear vs Random Forest

Projeto de aprendizado de máquina supervisionado que usa dados reais de cronometragem da F1 para prever o tempo de volta a partir do desgaste do pneu, do composto, da volta da corrida, das condições de pista e da equipe.

## Objetivo

O tempo de volta resulta de efeitos que atuam ao mesmo tempo: o carro fica mais rápido conforme queima combustível e mais lento conforme o pneu desgasta. Além disso, cada composto (macio, médio e duro) desgasta em um ritmo diferente.

O projeto compara como três modelos lidam com essa interação entre desgaste e composto:

1. **Regressão Linear:** assume o mesmo desgaste para todos os compostos
2. **Regressão Linear com interação:** recebe variáveis criadas manualmente para permitir um desgaste diferente por composto
3. **Random Forest Regressor:** aprende as interações sozinho

## Dados

Voltas do **GP do Bahrein de 2024**, obtidas com a biblioteca [FastF1](https://docs.fastf1.dev/), que acessa os dados oficiais de cronometragem da F1. A corrida pode ser trocada no início do notebook.

## Metodologia

1. **Coleta:** voltas da corrida cruzadas com os dados de clima usando `merge_asof`
2. **Limpeza:** remoção da primeira volta, voltas de box, voltas sob safety car ou bandeira amarela, pneus de chuva e voltas 7% mais lentas que a mais rápida
3. **Análise exploratória:** efeito do combustível, curvas de desgaste por composto e ritmo por equipe
4. **Preparação:** one-hot encoding de composto e equipe
5. **Modelagem:** baseline, Regressão Linear, Regressão Linear com interação (via `FunctionTransformer` no pipeline) e Random Forest
6. **Avaliação:** MAE, RMSE e R² no teste, validação cruzada com 5 folds e verificação de overfitting
7. **Interpretação:** coeficientes em segundos, importância das variáveis e simulação da curva de degradação aprendida por cada modelo

## Resultados

<!-- Preencha com os valores obtidos ao executar o notebook -->

| Modelo | MAE (s) | RMSE (s) | R² |
|---|---|---|---|
| Baseline (média) | | | |
| Regressão Linear | | | |
| Regressão Linear + interação | | | |
| Random Forest | | | |

## Principais conclusões

<!-- Escreva com suas palavras o que os resultados mostraram -->

## Limitações

- Uma única corrida, então o modelo aprende as condições específicas daquele circuito e daquele dia
- A divisão aleatória por volta deixa as métricas otimistas, porque voltas vizinhas do mesmo piloto são muito parecidas
- Não considera tráfego, DRS, modo de motor ou diferenças entre pilotos da mesma equipe

## Tecnologias

Python · FastF1 · pandas · NumPy · scikit-learn · matplotlib · seaborn

## Como executar

```bash
git clone https://github.com/SaulinS/f1-tempo-de-volta.git
cd f1-tempo-de-volta
pip install -r requirements.txt
jupyter notebook tempo_de_volta_f1.ipynb
```

Também funciona no Google Colab: basta fazer upload do notebook e executar todas as células.

Na primeira execução, a FastF1 baixa os dados da corrida e guarda em cache na pasta `cache_fastf1/`.

## Próximos passos

- Validação com `GroupKFold` por piloto
- Combinar várias corridas, incluindo o circuito como variável
- Criar variáveis a partir da telemetria (velocidade, acelerador, freio)
- Testar gradient boosting (XGBoost, LightGBM)

## Autor

**Saulo Sousa Cunha** · Engenharia de Computação, Universidade SENAI CIMATEC · Membro da TecRacing (Fórmula SAE)
[LinkedIn](https://linkedin.com/in/saulo-sousa-b2187b256) · [GitHub](https://github.com/SaulinS)
