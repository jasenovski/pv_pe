# Detecção de Cruzamento de Retas - Algoritmo PV-PE

## Descrição

Este projeto implementa um algoritmo para detectar e classificar cruzamentos entre retas em um espaço 2D. O algoritmo é capaz de determinar não apenas se duas retas se cruzam, mas também a direção do cruzamento, sendo útil para aplicações de rastreamento de objetos e análise de movimento.

## Estrutura do Projeto

```
cruzamento_retas/
├── README.md                    # Este arquivo
├── pv_pe_comentado.ipynb        # Notebook principal com implementação
├── pv_pe.html                   # Versão HTML do notebook
└── pontos.pkl                   # Dados de teste com trajetória de objeto
```

## Problema Abordado

O projeto resolve o problema de **detecção de cruzamento entre segmentos de reta**, especificamente:

1. **Reta Fixa (Contador)**: Um segmento que atua como "linha de contagem"
2. **Retas Móveis (Andante)**: Segmentos formados pelo movimento de um objeto
3. **Detecção**: Identificar quando as retas se cruzam
4. **Classificação**: Determinar a direção do cruzamento (A→B ou B→A)

## Conceitos Matemáticos

### Modelagem Vetorial

O algoritmo utiliza álgebra linear para resolver o sistema:
- **Reta Contador**: Definida pelos pontos C e D
- **Reta Andante**: Definida pelos pontos A e B

### Sistema de Equações

Para detectar cruzamento, resolve-se o sistema:
```
P = C + k₁(D - C) = A + k₂(B - A)
```

Onde:
- `k₁` e `k₂` são parâmetros escalares
- Cruzamento ocorre quando `0 ≤ k₁ ≤ 1` e `0 ≤ k₂ ≤ 1`

### Determinação da Direção

A direção é calculada usando produto escalar:
- **v₁**: Vetor contador (D - C)
- **v₂**: Vetor andante (B - A)  
- **v₃**: Vetor perpendicular a v₁

Se `v₂ · v₃ > 0`: Direção B→A  
Se `v₂ · v₃ < 0`: Direção A→B

## Implementação Detalhada

### 1. Carregamento de Dados
```python
# Carrega trajetória de um objeto em movimento
with open("pontos.pkl", "rb") as f:
    pontos = pkl.load(f)
```

### 2. Definição da Linha Contador
```python
pontos_contador = {
    "p1": np.array([320, 50]),   # Ponto superior
    "p2": np.array([320, 450]),  # Ponto inferior
}
```

### 3. Visualização Interativa
- Mostra cada ponto da trajetória frame a frame
- Desenha a linha contador em vermelho
- Desenha o ponto atual do objeto em verde

### 4. Pareamento de Pontos
```python
from itertools import pairwise
pontos_pareados = list(pairwise(pontos))
```

### 5. Detecção de Cruzamento
Para cada par de pontos consecutivos:
1. Constrói matriz do sistema linear
2. Calcula inversa da matriz
3. Resolve para k₁ e k₂
4. Verifica condições de cruzamento

### 6. Classificação da Direção
- Calcula produto escalar para determinar direção
- Classifica como "A→B" ou "B→A"

## Estrutura do Código

### Funções Principais

1. **Carregamento de Dados**: Lê arquivo pickle com trajetória
2. **Visualização**: Mostra movimento frame a frame
3. **Pareamento**: Cria segmentos consecutivos da trajetória
4. **Detecção**: Algoritmo principal de cruzamento
5. **Classificação**: Determina direção do movimento

### Tratamento de Casos Especiais

- **Matrizes Singulares**: Retas paralelas (sem cruzamento)
- **Segmentos Disjuntos**: k₁ ou k₂ fora do intervalo [0,1]
- **Precisão Numérica**: Uso de álgebra linear robusta

## Parâmetros Configuráveis

| Parâmetro | Valor Padrão | Descrição |
|-----------|--------------|-----------|
| `pontos_contador["p1"]` | [320, 50] | Ponto superior da linha contador |
| `pontos_contador["p2"]` | [320, 450] | Ponto inferior da linha contador |
| Frame dimensions | 480×640 | Dimensões da tela de visualização |

## Dependências

```python
import numpy as np          # Álgebra linear
import cv2                 # Visualização e processamento de imagem
import pickle as pkl       # Carregamento de dados
from itertools import pairwise  # Pareamento de pontos consecutivos
```

## Como Executar

1. **Preparação do Ambiente**:
   ```bash
   pip install numpy opencv-python
   ```

2. **Execução do Notebook**:
   - Abra `pv_pe_comentado.ipynb` no Jupyter ou VS Code
   - Execute as células sequencialmente
   - O arquivo `pontos.pkl` deve estar no mesmo diretório

3. **Interação**:
   - Pressione qualquer tecla para avançar frame a frame
   - Pressione 'q' para sair da visualização

## Saídas do Sistema

- **Visualização**: Animação mostrando movimento e linha contador
- **Console**: Mensagens indicando cruzamentos e direções
- **Classificação**: "cruzou A→B" ou "cruzou B→A"
