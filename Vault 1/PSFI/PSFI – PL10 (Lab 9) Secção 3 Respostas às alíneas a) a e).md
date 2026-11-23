
## Sistema original

O sistema é um FIR causal de 4.ª ordem com zeros em α, α*, 1/α e 1/α*, onde α = r·e^{jθ}, r = 0.8 e θ = π/3.

Os zeros ficam sobre dois ângulos:

| Ângulo | Zero de raio 0.8 | Zero de raio 1.25 |
|---|---|---|
| +60° (+π/3) | α | 1/α* |
| −60° (−π/3) | α* | 1/α |

Os coeficientes obtidos com `poly` são:

b = [1, −2.05, 3.2025, −2.05, 1]

Confirmação à mão: (1 − 0.8z⁻¹ + 0.64z⁻²)(1 − 1.25z⁻¹ + 1.5625z⁻²) = 1 − 2.05z⁻¹ + 3.2025z⁻² − 2.05z⁻³ + z⁻⁴.

Características do sistema original:
- h[n] é real, porque os zeros estão em pares conjugados;
- h[n] é simétrica, porque os zeros estão em pares recíprocos, logo o sistema tem fase linear;
- o atraso de grupo é constante e igual a N/2 = 4/2 = 2 amostras;
- |H| é mínima perto de ω = π/3 (onde estão os zeros) e máxima em ω = π (|H| ≈ 9.3).

![[Pasted image 20261123002008.png]]

## Conceitos usados nas respostas

Reflexão conjugada-recíproca: trocar um zero z₀ por 1/z₀* mantém o ângulo e inverte o raio (0.8 ↔ 1.25). A magnitude só muda por um fator constante:

|1 − (1/z₀*)·e^{−jω}| = (1/|z₀|)·|1 − z₀·e^{−jω}|

Normalizando o ganho (por exemplo, para o mesmo ganho em DC), a magnitude fica exatamente igual.

Condições para classificar os sistemas:

| Propriedade                | Condição nos zeros               | Condição em h[n]                |
| -------------------------- | -------------------------------- | ------------------------------- |
| Fase linear (generalizada) | pares {z₀, 1/z₀*}                | h[n] = c·h*[N−n], com \|c\| = 1 |
| Fase mínima                | todos dentro do círculo unitário | energia concentrada no início   |
| Fase máxima                | todos fora do círculo unitário   | energia concentrada no fim      |
| Resposta real              | pares conjugados {z₀, z₀*}       | h[n] real                       |

## Alínea a) Quantos sistemas têm a mesma magnitude?

Resposta: 9 sistemas, incluindo o original (8 diferentes do original).

Justificação:
- Cada um dos 4 zeros pode ser refletido ou não, o que dá 2⁴ = 16 combinações. Todas têm a mesma magnitude.
- A reflexão mantém o ângulo, por isso em cada ângulo há sempre dois zeros, cada um com raio 0.8 ou 1.25.
- Em cada ângulo só há 3 conjuntos diferentes de raios: {0.8, 0.8}, {0.8, 1.25} e {1.25, 1.25}.
- Muitas das 16 combinações repetem-se. Por exemplo, a +60°, não refletir nada dá {0.8, 1.25} e refletir os dois zeros dá {1.25, 0.8}, que é o mesmo conjunto.
- Como os dois ângulos são independentes, o número de sistemas distintos é 3 × 3 = 9.

Os 9 sistemas organizam-se numa grelha:

| +60° \ −60° | {0.8, 0.8} | {0.8, 1.25} | {1.25, 1.25} |
|---|---|---|---|
| {0.8, 0.8} | fase mínima, real | complexo | complexo |
| {0.8, 1.25} | complexo | ORIGINAL: fase linear, real | complexo |
| {1.25, 1.25} | complexo | complexo | fase máxima, real |

Resultado do MATLAB: 16 combinações de reflexão e 9 sistemas efetivamente distintos.

Nota: a contagem considera o ganho fixo. Multiplicar h[n] por uma constante e^{jφ} também mantém a magnitude, mas são variantes triviais e não se contam.

## Alínea b) Quantos são de fase linear?

Resposta: 1, que é o próprio sistema original.

Justificação:
- A fase linear exige zeros em pares {z₀, 1/z₀*}: cada zero tem de estar acompanhado do seu recíproco conjugado.
- Neste caso, isso obriga a ter um zero a 0.8 e outro a 1.25 em cada ângulo, ou seja {0.8, 1.25} a +60° e a −60°.
- Só o sistema original (o centro da grelha) cumpre esta condição. Em qualquer outro, há pelo menos um ângulo com {0.8, 0.8} ou {1.25, 1.25}, e um desses zeros fica sem o seu par.
- Verificação: os coeficientes do original são simétricos, [1, −2.05, 3.2025, −2.05, 1], e o atraso de grupo é constante e igual a 2 amostras.

Resultado do MATLAB: 1 sistema de fase linear.

## Alínea c) Quantos são de fase mínima?

Resposta: 1.

Justificação:
- A fase mínima exige todos os zeros dentro do círculo unitário (|z| < 1).
- Só há uma configuração assim: {0.8, 0.8} nos dois ângulos, ou seja zeros duplos em 0.8·e^{±jπ/3}.
- H_min(z) = K·(1 − 0.8z⁻¹ + 0.64z⁻²)². Com o mesmo ganho em DC que o original, h_min = [1.5625, −2.5, 3, −1.6, 0.64].
- Dos 9 sistemas, é o que tem menor atraso de grupo e concentra a energia no início de h[n]. É a mesma transformação que o código do LAB 3 aplica ao `hfilter`.
- Por simetria, há também 1 sistema de fase máxima (todos os zeros a 1.25): h_max = [0.64, −1.6, 3, −2.5, 1.5625], que é h_min invertido no tempo.

Resultado do MATLAB: 1 sistema de fase mínima.

## Alínea d) Quantos têm resposta impulsional real?

Resposta: 3.

Justificação:
- h[n] é real se e só se os zeros vierem em pares conjugados {z₀, z₀*}.
- Isto obriga o ângulo −60° a ter o mesmo conjunto de raios que o ângulo +60° (a diagonal da grelha).
- Há 3 casos:

| Configuração (igual nos dois ângulos) | Sistema |
|---|---|
| {0.8, 0.8} | fase mínima |
| {0.8, 1.25} | original (fase linear) |
| {1.25, 1.25} | fase máxima |

- Os outros 6 sistemas têm h[n] complexa e formam 3 pares de sistemas conjugados entre si: se um tem h[n], o outro tem h*[n], com os zeros espelhados em relação ao eixo real.

Resultado do MATLAB: 3 sistemas com resposta impulsional real.

## Alínea e) Dois sistemas diferentes com a mesma magnitude

Procedimento usado no MATLAB:
1. Partir dos zeros originais e refletir alguns deles (z → 1/z*).
2. Reconstruir os coeficientes com `poly`.
3. Normalizar o ganho para que o ganho em DC seja igual ao do original. Isto repõe o fator |z₀| perdido na reflexão e elimina uma fase constante arbitrária.
4. Calcular a resposta em frequência com `freqz` e comparar a magnitude com a do original.

Sistemas escolhidos:

| | Zeros a +60° | Zeros a −60° | h[n] |
|---|---|---|---|
| Alternativo 1 | {0.8, 0.8} | {0.8, 1.25} | complexa |
| Alternativo 2 | {0.8, 1.25} | {0.8, 0.8} | complexa |

- O alternativo 1 obtém-se refletindo o zero 1/α* (raio 1.25 a +60°), que passa para α.
- O alternativo 2 obtém-se refletindo o zero 1/α (raio 1.25 a −60°), que passa para α*.

Coeficientes obtidos:
- Alternativo 1: [1.1607 − 0.4639j, −1.9375 + 1.2990j, 2.7107 − 1.5032j, −1.5743 + 0.9650j, 0.7429 − 0.2969j]
- Alternativo 2: [1.1607 + 0.4639j, −1.9375 − 1.2990j, 2.7107 + 1.5032j, −1.5743 − 0.9650j, 0.7429 + 0.2969j]

Os dois sistemas são conjugados um do outro: h₂[n] = h₁*[n].

Resultados:
- erro máximo de magnitude do alternativo 1: 7.1 × 10⁻¹⁵;
- erro máximo de magnitude do alternativo 2: 5.3 × 10⁻¹⁵.

Estes erros são da ordem da precisão numérica do MATLAB, por isso as magnitudes são exatamente iguais.

Interpretação da figura:
- Zeros: estão em posições diferentes nos três sistemas.
- h[n]: o original é real e simétrico; os alternativos têm parte imaginária.
- Magnitude: as três curvas sobrepõem-se completamente.
- Fase: a do original é uma reta (fase linear); as dos alternativos não.
- Atraso de grupo: no original é constante e igual a 2. No alternativo 1 tem um pico negativo (≈ −7) em ω = π/3, e no alternativo 2 em ω = 5π/3. Nos dois casos, é o ângulo onde o zero de raio 1.25 foi trocado por um de raio 0.8: 2 − 0.8/0.2 − 1.25/0.25 = 2 − 4 − 5 = −7.
- Como os alternativos são conjugados, H₂(e^{jω}) = H₁*(e^{−jω}). Os gráficos de um são os do outro espelhados em torno de ω = π.
- Nos gráficos usou-se `'whole'` (0 a 2π): em sistemas complexos a magnitude não é simétrica em torno de π, por isso é preciso verificar o círculo completo.

Conclusão: os dois sistemas têm exatamente a mesma magnitude que o original, mas são sistemas diferentes: os zeros, h[n], a fase e o atraso de grupo mudam. A magnitude, sozinha, não define um FIR de forma única.


![[Pasted image 20261123002238.png]]

![[Pasted image 20261123002301.png|700]]![[Pasted image 20261123002320.png]]

![[Pasted image 20261123002409.png]]