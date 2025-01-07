# Freqtrade_Confluence_Strategy
Robô de trade configurado para o freqtrade 
IntegrativeConfluenceStrategy: Documentação Completa (README)

# Descrição

A IntegrativeConfluenceStrategy é uma estratégia de trading para o Freqtrade, projetada para capturar reversões usando Bollinger Bands, RSI, ADX e Volume como critérios principais. Ela implementa uma abordagem clara e precisa de gerenciamento de riscos:
	1.	Take Profit (TP) é definido primeiro, utilizando Bollinger Bands, pivôs recentes e fallback com ATR.
	2.	Stop Loss (SL) é calculado para garantir um mínimo Risk-Reward (R:R), descartando trades inviáveis.
	3.	Time-Based Stop encerra trades que ultrapassam um limite configurável de duração.

Essa estratégia é configurada para timeframe de 1h e não requer validações em timeframes adicionais.

# Características
	•	Foco em reversões extremas:
	•	LONG: close < bb_lower
	•	SHORT: close > bb_upper
	•	Filtros principais:
	•	RSI para detectar condições de sobrecompra/sobrevenda.
	•	ADX para confirmar força da tendência.
	•	Volume para validar reversões.
	•	Gestão de Riscos:
	•	Take Profit (TP) definido antes.
	•	Stop Loss (SL) ajustado dinamicamente para atender ao R:R mínimo (rr_min).
	•	SL limitado em distância máxima e mínima com base no ATR.
	•	Rejeita trades inviáveis.
	•	Custom Stoploss:
	•	Garante saídas claras (TP, SL ou Time-Based Stop).

# Parâmetros
	•	Bollinger Bands:
	•	bb_period: Período para cálculo das bandas (ex.: 20).
	•	bb_dev: Desvio padrão para definir as bandas superior/inferior (ex.: 2.0).
	•	RSI:
	•	rsi_period: Período para cálculo do RSI (ex.: 14).
	•	rsi_lower / rsi_upper: Limites para sobrecompra/sobrevenda (ex.: 30 e 70).
	•	ADX:
	•	adx_thr: Limite mínimo para confirmar tendência (ex.: 25).
	•	Volume:
	•	vol_ma_len: Período para média de volume (ex.: 24).
	•	vol_mult: Multiplicador para validar se o volume atual é significativo (ex.: 1.2).
	•	ATR (Average True Range):
	•	atr_period: Período para cálculo do ATR (ex.: 14).
	•	atr_factor: Fallback para calcular TP baseado no ATR (ex.: 2.0).
	•	max_stop_factor: Limite superior para SL em função do ATR (ex.: 3.0).
	•	min_stop_factor: Distância mínima para SL em função do ATR (ex.: 0.5).
	•	Risk-Reward (R:R):
	•	rr_min: Mínimo R:R permitido (ex.: 2.0 => 1:2).
	•	Time-Based Stop:
	•	max_trade_duration_in_hours: Tempo máximo de duração do trade (ex.: 48h).
	•	Fibonacci:
	•	use_fib: (Opcional) Incluir Fibonacci como candidato para TP.

# Lógica de Funcionamento
	1.	Take Profit (TP):
	•	Calculado primeiro, com base em:
	•	Bollinger Bands:
	•	LONG: bb_upper como resistência.
	•	SHORT: bb_lower como suporte.
	•	High/Low Recentes:
	•	Últimos 20 candles para determinar níveis pivot.
	•	ATR Fallback:
	•	LONG: close + atr_factor * ATR.
	•	SHORT: close - atr_factor * ATR.
	•	Fibonacci (opcional):
	•	LONG: recent_low + 1.618 * (recent_high - recent_low).
	•	SHORT: recent_high - 1.618 * (recent_high - recent_low).
	2.	Stop Loss (SL):
	•	Calculado dinamicamente para atender ao R:R mínimo (rr_min).
	•	Validado:
	•	Distância máxima: Não pode exceder atr * max_stop_factor.
	•	Distância mínima: Não pode ser menor que atr * min_stop_factor.
	•	Se inválido (SL muito curto/longo), o trade é rejeitado.
	3.	Filtros de Entrada:
	•	Bollinger Bands:
	•	LONG: close < bb_lower.
	•	SHORT: close > bb_upper.
	•	RSI:
	•	LONG: rsi < rsi_upper.
	•	SHORT: rsi > rsi_lower.
	•	ADX:
	•	Apenas entradas com adx > adx_thr.
	•	Volume:
	•	Confirma reversões com volume acima da média (volume > vol_ma * vol_mult).
	4.	Saída:
	•	Take Profit (TP).
	•	Stop Loss (SL).
	•	Time-Based Stop: Encerra trades com duração superior ao limite configurado (max_trade_duration_in_hours).

# Código

O código completo está disponível no arquivo IntegrativeConfluenceStrategy.py. Ele inclui:
	•	Funções de cálculo de TP e SL: calc_takeprofit_stoploss_v4.
	•	Definição de indicadores: populate_indicators.
	•	Lógica de entrada: populate_entry_trend.
	•	Controle de saída: custom_stoploss.

# Exemplo de Configuração

{
  "strategy": "IntegrativeConfluenceStrategy",
  "timeframe": "1h",
  "stake_currency": "USDT",
  "stake_amount": "unlimited",
  "max_open_trades": 5,
  "custom_stoploss": true,
  "custom_entry_point": true
}

# Observações
	1.	Ajuste de Parâmetros:
	•	Para mais trades:
	•	Use close < bb_mid (LONG) e close > bb_mid (SHORT).
	•	Diminua adx_thr e vol_mult.
	•	Reduza rr_min para aceitar R:R menor.
	•	Para trades mais curtos:
	•	Aumente rr_min e atr_factor.
	•	Reduza max_trade_duration_in_hours.
	2.	Debug:
	•	Ative logs (debug_logs=True) para verificar o cálculo de TP e SL.
	•	Utilize print ou logger.info nos pontos de cálculo.

///Essa estratégia é flexível e pode ser ajustada para diferentes mercados e condições. Ajuste os parâmetros para encontrar o equilíbrio ideal entre frequência e qualidade dos trades.
