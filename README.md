# Estimativa de Altura e Geometria 3D a partir de Imagens Top-Down

## Objetivo

Estimar a altura, posição e orientação de objetos (casas, prédios, pontes, etc.) a partir de imagens aéreas/satélite (top-down), utilizando:

- Intensidade de luz (RGB)
- Sombras
- Posição do sol (tempo + geolocalização)
- Deep Learning + dados sintéticos

---

# 1. Modelo de Formação da Imagem

A intensidade de um pixel é dada por:

\[
I(x, y) = \rho(x, y) \cdot \max(0, \mathbf{n}(x, y) \cdot \mathbf{s}) + I_{shadow}
\]

Onde:

- \( I(x,y) \): intensidade observada
- \( \rho(x,y) \): refletância (albedo)
- \( \mathbf{n}(x,y) \): normal da superfície
- \( \mathbf{s} \): direção do sol
- \( I_{shadow} \): componente de sombra

---

# 2. Direção da Luz (Sol)

A direção da luz depende de:

- Latitude \( \phi \)
- Longitude \( \lambda \)
- Tempo \( t \)

\[
\mathbf{s} =
\begin{bmatrix}
\cos(\theta_z)\sin(\gamma) \\
\cos(\theta_z)\cos(\gamma) \\
\sin(\theta_z)
\end{bmatrix}
\]

Onde:

- \( \theta_z \): elevação solar
- \( \gamma \): azimute solar

---

# 3. Altura via Sombra

\[
h = L \cdot \tan(\theta_z)
\]

- \( h \): altura do objeto
- \( L \): comprimento da sombra
- \( \theta_z \): ângulo do sol

---

# 4. Comprimento da Sombra

\[
L = \sqrt{(x_s - x_o)^2 + (y_s - y_o)^2}
\]

- \( (x_o, y_o) \): base do objeto
- \( (x_s, y_s) \): fim da sombra

---

# 5. Conversão Pixel → Mundo Real

\[
L_{real} = L_{pixel} \cdot S
\]

- \( S \): escala (metros/pixel)

---

# 6. Estimativa baseada em RGB

Sem depender explicitamente da sombra:

\[
h \approx f(I_R, I_G, I_B, \nabla I, \mathbf{s})
\]

- \( \nabla I \): gradiente (bordas/sombras)
- \( f \): função aprendida (rede neural)

---

# 7. Segmentação de Objetos

Cada objeto:

\[
O_i = \{(x, y) \in \Omega \mid \text{pixel pertence ao objeto } i\}
\]

Centroide:

\[
(x_c, y_c) = \frac{1}{|O_i|} \sum_{(x,y)\in O_i} (x,y)
\]

---

# 8. Orientação Geográfica

Direção da sombra:

\[
\theta_{shadow} = \arctan2(y_s - y_o, x_s - x_o)
\]

Orientação do objeto:

\[
\theta_{object} = \theta_{shadow} + \pi
\]

---

# 9. Modelo de Deep Learning

## Entrada

\[
X = \{I(x,y), \mathbf{s}, t, \phi, \lambda\}
\]

## Saída (por objeto)

\[
\hat{y}_i =
\begin{cases}
h_i \\
(x_i, y_i) \\
\theta_i \\
\text{classe}_i
\end{cases}
\]

---

# 10. Função de Perda

\[
\mathcal{L} =
\alpha \cdot \|h - \hat{h}\|^2 +
\beta \cdot \|\theta - \hat{\theta}\|^2 +
\gamma \cdot \text{IoU}_{loss} +
\delta \cdot \text{CrossEntropy}
\]

Componentes:

- Erro de altura
- Erro de orientação
- Erro de segmentação
- Erro de classificação

---

# 11. Dados Sintéticos (Three.js)

Dataset:

\[
D = \{(X_i, Y_i)\}
\]

- \( X_i \): imagem renderizada
- \( Y_i \): labels
  - altura
  - posição
  - orientação
  - posição solar

---

# 12. Problema Formal

\[
f: \text{Imagem Top-Down} \rightarrow \text{Mapa 3D Semântico}
\]

\[
f(I) =
\{O_i, h_i, (x_i,y_i), \theta_i\}_{i=1}^N
\]

---

# 13. Natureza do Sistema

Sistema híbrido:

- Fotometria (luz/sombra)
- Geometria (trigonometria)
- Visão computacional
- Deep Learning
- Simulação 3D

---

# 14. Limitação Fundamental

Sem sombra ou metadados:

\[
h \text{ não é diretamente observável}
\]

→ Problema mal condicionado  
→ A rede aprende inferência estatística

---

# Resultado Final Esperado

Dado:

\[
I(x,y), t, \phi, \lambda
\]

O sistema retorna:

\[
\forall i:
\quad
(h_i, x_i, y_i, \theta_i, class_i)
\]
