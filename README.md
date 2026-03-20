# Estimativa de Altura e Geometria 3D a partir de Imagens Top-Down

## Objetivo

Estimar a altura, posição e orientação de objetos (casas, prédios, pontes, etc.) a partir de imagens aéreas/satélite (top-down), utilizando:

- Intensidade de luz (RGB)
- Sombras
- Posição do sol (tempo + geolocalização)
- Deep Learning + dados sintéticos

---

# 1. Modelo de Formação da Imagem

I(x, y) = rho(x, y) * max(0, dot(n(x, y), s)) + I_shadow

Onde:

- I(x,y): intensidade observada
- rho(x,y): refletância (albedo)
- n(x,y): normal da superfície
- s: direção do sol
- I_shadow: componente de sombra

---

# 2. Direção da Luz (Sol)

s = [
    cos(theta_z) * sin(gamma),
    cos(theta_z) * cos(gamma),
    sin(theta_z)
]

Onde:

- theta_z: elevação solar
- gamma: azimute solar

---

# 3. Altura via Sombra

h = L * tan(theta_z)

- h: altura do objeto
- L: comprimento da sombra
- theta_z: ângulo do sol

---

# 4. Comprimento da Sombra

L = sqrt((x_s - x_o)^2 + (y_s - y_o)^2)

- (x_o, y_o): base do objeto
- (x_s, y_s): fim da sombra

---

# 5. Conversão Pixel → Mundo Real

L_real = L_pixel * S

- S: escala (metros/pixel)

---

# 6. Estimativa baseada em RGB

h ≈ f(I_R, I_G, I_B, grad(I), s)

- grad(I): gradiente da imagem (bordas/sombras)
- f: função aprendida (rede neural)

---

# 7. Segmentação de Objetos

O_i = conjunto de pixels pertencentes ao objeto i

Centroide:

x_c = soma(x) / N  
y_c = soma(y) / N  

---

# 8. Orientação Geográfica

theta_shadow = atan2(y_s - y_o, x_s - x_o)

theta_object = theta_shadow + PI

---

# 9. Modelo de Deep Learning

## Entrada

X = {imagem, direção do sol, tempo, latitude, longitude}

## Saída (por objeto)

Para cada objeto i:

- h_i → altura
- (x_i, y_i) → posição
- theta_i → orientação
- class_i → tipo (casa, prédio, etc.)

---

# 10. Função de Perda

Loss = 
    alpha * erro_altura +
    beta * erro_orientacao +
    gamma * erro_segmentacao (IoU) +
    delta * erro_classificacao

---

# 11. Dados Sintéticos (Three.js)

Dataset:

D = {(imagem, labels)}

Labels:

- altura
- posição
- orientação
- posição do sol

---

# 12. Problema Formal

f(imagem) → mapa 3D semântico

Resultado:

Para cada objeto i:

{altura, posição, orientação}

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

→ altura não é diretamente observável  
→ problema mal condicionado  
→ o modelo aprende por inferência estatística  

---

# Resultado Final Esperado

Entrada:

imagem + tempo + latitude + longitude

Saída:

Para cada objeto:

- altura
- posição
- orientação
- classe

---

# Interpretação

Você está construindo um sistema que:

- Reconstrói informação 3D a partir de imagem 2D
- Usa física (sombras e trigonometria)
- Usa aprendizado de máquina (Deep Learning)
- Generaliza de dados sintéticos para o mundo real

---
