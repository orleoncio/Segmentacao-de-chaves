# KeyVision — Análise e Comparação de Perfis de Chaves

Projeto experimental de **Processamento Digital de Imagens (PDI)** e **Visão Computacional** desenvolvido em Python com OpenCV e Jupyter Notebook. O objetivo é identificar chaves em fotografias, extrair suas características geométricas e comparar visualmente seus perfis.

> **Escopo:** protótipo acadêmico para análise de silhuetas. Os resultados não comprovam compatibilidade mecânica entre chaves nem substituem uma inspeção dimensional calibrada.

## Objetivos

- Segmentar a chave em relação ao fundo da imagem.
- Extrair o contorno e características geométricas.
- Estimar a orientação da chave e alinhar imagens para comparação.
- Quantificar a semelhança entre máscaras e contornos.
- Destacar visualmente regiões coincidentes e divergentes, com atenção à área dos dentes.

## Tecnologias

- **Python** — implementação do pipeline.
- **OpenCV** — filtros, limiarização, morfologia, contornos, PCA e transformações geométricas.
- **NumPy** — manipulação de matrizes e operações lógicas.
- **Matplotlib** — visualização das etapas e resultados.
- **SciPy** — cálculo da distância de Hausdorff.
- **Jupyter Notebook** — experimentação e documentação.

## Pipeline de processamento

```text
Fotografia da chave
       ↓
Escala de cinza e filtro gaussiano
       ↓
Limiarização automática (Otsu)
       ↓
Operações morfológicas
       ↓
Extração do contorno principal
       ↓
Máscara binária da chave
       ↓
Estimativa de orientação (PCA) e rotação
       ↓
Recorte, centralização e normalização
       ↓
Comparação: IoU e distância de Hausdorff
       ↓
Visualização das diferenças
```

### 1. Segmentação e extração de contornos

A imagem é convertida para escala de cinza, suavizada com filtro gaussiano e binarizada pelo método de **Otsu**. Operações morfológicas ajudam a reduzir ruídos. O contorno principal é extraído com `cv2.findContours()` e usado para construir uma máscara da chave.

As características analisadas incluem área (`cv2.contourArea()`), perímetro (`cv2.arcLength()`), retângulo delimitador (`cv2.boundingRect()`) e centroide (`cv2.moments()`).

### 2. Alinhamento das imagens

A orientação é estimada por **Análise de Componentes Principais (PCA)** aplicada aos pixels da máscara. A imagem é rotacionada com `cv2.getRotationMatrix2D()` e `cv2.warpAffine()`, evitando cortes ao ampliar a área de saída.

Após a rotação, as máscaras são recortadas, redimensionadas preservando a proporção e centralizadas em uma área comum. A orientação da cabeça e da ponta deve ser conferida antes da comparação.

### 3. Comparação geométrica

**Intersection over Union (IoU):** mede a proporção entre a interseção e a união das áreas das duas máscaras.

\[
IoU(A,B)=\frac{|A\cap B|}{|A\cup B|}
\]

**Distância de Hausdorff:** mede o maior afastamento entre os contornos, considerando o ponto mais próximo no contorno oposto. Valores menores indicam maior proximidade geométrica.

A visualização final diferencia regiões comuns, exclusivas da chave A e exclusivas da chave B. Também é possível selecionar uma **região de interesse (ROI)** para examinar os dentes separadamente.

## Como executar

1. Clone o repositório e entre na pasta do projeto.
2. Crie um ambiente virtual Python.
3. Instale as dependências:

```bash
pip install opencv-python numpy matplotlib scipy jupyter ipykernel
```

4. Inicie o Jupyter:

```bash
jupyter notebook
```

5. Abra o notebook do projeto e ajuste os caminhos das imagens de entrada às pastas do seu repositório.
6. Execute as células na ordem, verificando as imagens intermediárias e os resultados da comparação.

> Os nomes dos notebooks e os caminhos das imagens devem ser ajustados à estrutura real do repositório.

## Experimentos sugeridos

| Teste | Finalidade |
|---|---|
| Mesma máscara comparada consigo mesma | Verificar IoU = 1 e Hausdorff = 0 |
| Mesma chave fotografada em ângulos diferentes | Avaliar a robustez do alinhamento |
| Duas chaves diferentes | Observar diferenças de silhueta e contorno |
| Comparação apenas da região serrilhada | Investigar diferenças locais nos dentes |

## Limitações

- A segmentação depende da iluminação, do contraste e do fundo.
- A PCA estima a orientação da silhueta completa; a cabeça da chave pode influenciar o eixo calculado.
- O alinhamento não identifica automaticamente qual extremidade é a ponta.
- A normalização independente de escala pode ocultar diferenças reais de tamanho.
- IoU e Hausdorff são sensíveis a erros de alinhamento e segmentação.
- Uma alta similaridade visual não garante que duas chaves sejam funcionalmente equivalentes.

## Próximas etapas

- Identificar automaticamente a lâmina e a região serrilhada.
- Alinhar as chaves por referências geométricas estáveis.
- Calibrar a câmera para medições em milímetros.
- Avaliar tolerâncias e possíveis deformações com imagens controladas.
- Construir um conjunto de testes com métricas e casos de falha documentados.

## Contexto

Projeto de estudo e portfólio voltado à aplicação de técnicas clássicas de PDI e Visão Computacional em problemas de inspeção geométrica.
