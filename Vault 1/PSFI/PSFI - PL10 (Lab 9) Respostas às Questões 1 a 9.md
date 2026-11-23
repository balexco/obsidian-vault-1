
Filtros testados: diferenciador FIR e transformador de Hilbert FIR, ambos de fase linear, ordem 80 (N = 81 coeficientes), com F_S = 8 kHz.

Ideia-chave para todo o lab: N = 81 é ímpar e os coeficientes são antissimétricos (h[n] = −h[80−n]). Por isso os dois filtros são FIR de fase linear de tipo III, o que implica:
- |H| = 0 obrigatoriamente em ω = 0 (0 Hz) e em ω = π (4000 Hz);
- fase = ±90° mais um atraso constante de (N−1)/2 = 40 amostras = 5 ms.

## Questão 1

<p align="center">
  <img src="PSFI/1.png" width="700" />
</p>

- Ideal: H(e^{jω}) = jω, logo |H| = ω. A magnitude cresce linearmente com a frequência, de 0 até π.
- FIR de ordem 80: segue a reta ideal em quase toda a banda (de cerca de 50 Hz a 3.8 kHz). Tem um máximo de cerca de 3 perto de 3.8 kHz e depois cai para 0 em 4000 Hz. Esta queda é imposta pelo tipo III (zero em z = −1), por isso o filtro não chega ao valor ideal π em ω = π.
- Eixos equivalentes: freq (Hz) = (ω/π) × F_N = (ω/π) × 4000.

| ω/π | 0 | 0.25 | 0.5 | 0.75 | 1 |
|---|---|---|---|---|---|
| freq (Hz) | 0 | 1000 | 2000 | 3000 | 4000 |

## Questão 2

<p align="center">
  <img src="PSFI/2.png" width="700" />
</p>

- Ideal: H(e^{jω}) = −j·sgn(ω), logo |H| = 1 para 0 < ω < π. Deixa a amplitude igual e só desfasa −90°.
- FIR de ordem 80: |H| ≈ 1 entre cerca de 130 Hz e 3870 Hz, e |H| = 0 em 0 Hz e em 4000 Hz (tipo III), com bandas de transição junto aos dois extremos. A resposta é simétrica em torno de 2000 Hz (ω = π/2).
- Consequência prática: os sinais muito perto de 0 Hz ou de 4 kHz são atenuados. A 60 Hz o ganho é só cerca de 0.6, e é por isso que o lab testa entre 60 Hz e 3.8 kHz.

## Questão 3

Resposta: alternativa C, `xref = x[DELAY];`

- Um FIR de fase linear com N = 81 tem H(e^{jω}) = e^{−jω·40}·[j·A(ω)]. O filtro aplica a operação pretendida (derivada ou −90°) e um atraso de (N−1)/2 = 40 amostras (5 ms).
- No buffer, `x[i]` = x(n−i), porque `yn` e `xref` são calculados antes do deslocamento. Logo `x[DELAY]` = x(n−40).
- Para comparar entrada e saída no osciloscópio, a entrada tem de ser atrasada exatamente o mesmo que o filtro atrasa. Assim, a única diferença entre os dois canais é o efeito do filtro.
- Alternativas erradas:
  - A (`x[0]`) e E (`x[N-1]`) ficam com 40 amostras de diferença;
  - B e D ficam com 1 amostra de diferença, o que dá um erro de fase de ω. A 2500 Hz isso já são 112.5°.

## Questão 4

Resposta: 20 multiplicações.

1. Metade dos coeficientes é nula (de 81 para 40). A resposta ideal é h[m] = (1 − cos πm)/(πm), com m = n − 40. Para m par (incluindo m = 0), h[m] = 0. Basta inspecionar o `FPS_HT.h`: os coeficientes alternam com zeros. Ficam 40 coeficientes não nulos.
2. Antissimetria (de 40 para 20). Como h[40+m] = −h[40−m], cada par pode juntar-se assim: h[40+m]·(x[40+m] − x[40−m]). Primeiro subtrai-se, depois multiplica-se uma só vez. Os 40 coeficientes formam 20 pares, logo 20 multiplicações.

```c
yn = 0.0;
for (k = 1; k <= DELAY; k += 2)     // k = 1, 3, ..., 39  ->  20 multiplicações
    yn += hHT[DELAY + k] * (x[DELAY + k] - x[DELAY - k]);
```

Para comparação: o diferenciador também é antissimétrico, mas só tem o coeficiente central nulo, por isso fica em 40 multiplicações.

## Questão 5

Amarelo (CH1) = entrada x[n] (atrasada, `xref`). Verde (CH2) = saída y[n] do diferenciador.

- Onda quadrada: a derivada de uma onda quadrada é zero nos patamares e um impulso em cada transição: positivo nas subidas e negativo nas descidas. É exatamente o que aparece a verde.
- Onda triangular: a derivada de uma rampa é uma constante, positiva quando sobe e negativa quando desce. A derivada do triângulo é, portanto, uma onda quadrada, e é isso que aparece a verde. Tem pequena amplitude (escala de 50 mV/div) porque o declive do triângulo é pequeno.
- Conclusão: o resultado é consistente com um diferenciador. Os impulsos aparecem alinhados com as transições graças ao `xref = x[DELAY]`. O *ringing* a seguir aos impulsos acontece porque o diferenciador real é limitado em banda (|H| → 0 em 4 kHz) e não consegue produzir um impulso ideal.

## Questão 6

Amarelo (CH1) = entrada x[n] (`xref`). Verde (CH2) = saída y[n] do transformador de Hilbert.

- O Hilbert desfasa −90°, ou seja a saída fica atrasada um quarto de período em relação à entrada. Nas duas capturas, o verde atinge o máximo depois do amarelo, exatamente T/4 mais tarde:
  - a 200 Hz: T = 5 ms, logo T/4 = 1.25 ms (1.25 divisões a 1 ms/div);
  - a 2500 Hz: T = 0.4 ms, logo T/4 = 100 µs (1 divisão a 100 µs/div).
- As amplitudes são iguais (≈ 960 mV pico a pico), o que confirma |H| ≈ 1 na banda.
- Conclusão: o resultado é consistente com um transformador de Hilbert. O atraso em tempo muda com a frequência (1.25 ms contra 100 µs), mas o desfasamento é sempre 90°. É isto que distingue um Hilbert de um simples atraso.

## Questão 7

Resposta: um atraso puro de exatamente (N−1)/2 = 40 amostras, ou seja z^{−40} (5 ms a 8 kHz). No código C, este bloco é o `xref = x[DELAY]` da Questão 3.

- O Hilbert FIR tem H(e^{jω}) = −j·sgn(ω)·e^{−jω·40}. O termo e^{−jω·40} acrescenta uma fase −40ω, que depende da frequência.
- Se a referência sofrer o mesmo atraso z^{−40}, essa fase cancela-se na comparação, e a diferença de fase entre os canais fica −90° para qualquer frequência.
- Com qualquer outro atraso, a diferença de fase passaria a ser −90° + ω·(erro de atraso), que varia com a frequência.
- As cadeias D/A + AIF são iguais nos dois canais, por isso não alteram a fase relativa.

## Questão 8

Ajuste: desfasar cada harmónica de −90°, que é o que o Hilbert faz. Como sin(a − π/2) = −cos(a), isto equivale a trocar −sin(…) por +cos(…).

```matlab
%% PSFI PL 10 - RESOLUÇÃO QUESTÃO 8 DA SECÇÃO 7

% generate a 50Hz sawtooth wave

L = 15;

FS = 8000;

T = 1/FS;

t = [0:T:0.07-T];

freq = 50;

% In-phase

x = zeros(1,length(t));

for k = 1:L

x = x - sin(2*pi*k*freq*t)/k;

end

% Quadrature

y = zeros(1,length(t));

for k = 1:L

y = y + cos(2*pi*k*freq*t)/k;

end

figure('Name', 'Questão 8 - Secção 7 - PSFI PL10')

subplot(1,2,1)

plot(t,x)

xlabel('Time (s)'); ylabel('Amplitude'); title('In-phase')

axis([0 0.07 -2 2])

subplot(1,2,2)

plot(t,y)

xlabel('Time (s)'); ylabel('Amplitude'); title('Quadrature')
```

![[Pasted image 20261123001616.png]]

É consistente com a Figura 6? Sim. Nos dois casos a versão em quadratura do dente de serra é um trem de picos positivos, um em cada descontinuidade (salto) do dente de serra, com vales arredondados entre eles. A simulação usa 50 Hz e o lab 200 Hz, mas a forma é a mesma, e os picos verdes da Figura 6 aparecem alinhados com os saltos da onda amarela.

## Questão 9

- Figura 7 (simulação): o *ripple* resulta só de se truncar a série de Fourier às 15 primeiras harmónicas. Isso equivale a um passa-baixo ideal de fase nula (linear), que não desfasa as harmónicas umas em relação às outras. O efeito de Gibbs aparece igual dos dois lados de cada descontinuidade, tal como na *Filtered wave 1* do LAB 3 (filtro de fase linear).
- Figura 6 (experimental): o sinal passa pela cadeia real: filtro anti-aliasing (AAF) + A/D à entrada e D/A + filtro de reconstrução (AIF) à saída. Estes filtros analógicos são causais e não têm fase linear (comportam-se como sistemas de fase mínima). Cada harmónica sofre um atraso diferente (dispersão de fase), e as oscilações aparecem só depois de cada transição, tal como na *Filtered wave 2* do LAB 3 (filtro de fase mínima).
- Prova de que a causa é a cadeia analógica e não o Hilbert: a própria onda de entrada (amarela) na Figura 6 já tem o *ripple* só de um lado. O Hilbert e o atraso z^{−40} são de fase linear, por isso não criam assimetria.
- Ideia a reter (ligação ao LAB 3): a magnitude define *quanto* ripple existe, e a fase define *onde* ele aparece. Com fase linear o ripple fica simétrico; com fase não linear (mínima) fica concentrado depois das transições.
