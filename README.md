# Modelando o Tempo de Volta na Fórmula 1: Regressão Linear vs Random Forest

Projeto de aprendizado de máquina supervisionado que usa dados reais de cronometragem da F1 para prever o tempo de volta a partir do desgaste do pneu, do composto, da volta da corrida, das condições de pista e da equipe.

## Objetivo

O tempo de volta resulta de efeitos que atuam ao mesmo tempo: o carro fica mais rápido conforme queima combustível e mais lento conforme o pneu desgasta. Além disso, cada composto (macio, médio ou duro) pode desgastar em um ritmo diferente. Nesta corrida, após a limpeza dos dados, só restaram voltas com os compostos macio (SOFT) e duro (HARD).

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

Métricas no conjunto de teste (195 voltas) e MAE médio da validação cruzada com 5 folds no treino (779 voltas):

| Modelo | MAE (s) | RMSE (s) | R² | MAE CV (s) |
|---|---|---|---|---|
| Baseline (média) | 0,977 | 1,173 | −0,012 | — |
| Regressão Linear | 0,259 | 0,370 | 0,900 | 0,306 ± 0,017 |
| Regressão Linear + interação | 0,259 | 0,369 | 0,900 | 0,306 ± 0,017 |
| Random Forest | 0,286 | 0,394 | 0,886 | 0,304 ± 0,002 |

## Principais conclusões

- **Todos os modelos superam com folga o baseline:** o MAE cai de 0,98 s para cerca de 0,26 s no teste. O MAE da validação cruzada (≈ 0,31 s) é a estimativa mais confiável, já que a divisão aleatória por volta deixa o teste otimista.
- **A interação não trouxe ganho:** com só SOFT e HARD após a limpeza, os dados desta corrida não mostram uma diferença de desgaste entre compostos que o modelo aditivo não capte.
- **O Random Forest não superou a Regressão Linear:** foi pior no teste, empatou na validação cruzada e mostrou overfitting (R² de 0,980 no treino contra 0,886 no teste).
- **Os efeitos principais têm o sinal esperado:** ≈ +0,11 s por volta de uso do pneu e ≈ −0,07 s por volta de corrida (queima de combustível). Os coeficientes de temperatura não devem ser interpretados, por causa da colinearidade com o número da volta.

## Limitações

- Uma única corrida, então o modelo aprende as condições específicas daquele circuito e daquele dia
- A divisão aleatória por volta deixa as métricas otimistas, porque voltas vizinhas do mesmo piloto são muito parecidas
- Não considera tráfego, DRS, modo de motor ou diferenças entre pilotos da mesma equipe

## Tecnologias

Python · FastF1 · pandas · NumPy · scikit-learn · matplotlib · seaborn

> [!WARNING]
> **A FastF1 pode não funcionar em ambientes de nuvem, como o Google Colab.** Durante o desenvolvimento, o servidor de cronometragem da F1 retornou `403 Forbidden` para conexões vindas do Colab, e a FastF1 exibe isso como `SessionNotAvailableError`, mesmo para corridas que existem. Para baixar os dados, rode o notebook **localmente**. Os dados desta corrida já estão incluídos em `dados/`, então o notebook também funciona sem acesso ao servidor.

## Como executar

```bash
git clone https://github.com/SaulinS/Bahrein-Fast-Lap.git
cd Bahrein-Fast-Lap
pip install -r requirements.txt
jupyter notebook tempo_de_volta_f1.ipynb
```

O notebook lê os dados de `dados/bahrain_2024_voltas.parquet`. Se você trocar a corrida, a FastF1 baixa os dados (com cache em `cache_fastf1/`) e salva as colunas usadas em um novo arquivo dentro de `dados/`.

## Próximos passos

- Validação com `GroupKFold` por piloto
- Combinar várias corridas, incluindo o circuito como variável
- Criar variáveis a partir da telemetria (velocidade, acelerador, freio)
- Testar gradient boosting (XGBoost, LightGBM)

## Autor

**Saulo Sousa Cunha** · Engenharia de Computação, Universidade SENAI CIMATEC · Membro da TecRacing (Fórmula SAE)
[LinkedIn](https://linkedin.com/in/saulo-sousa-b2187b256) · [GitHub](https://github.com/SaulinS)
