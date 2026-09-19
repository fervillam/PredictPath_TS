# Introducción a las Ciencias Ómicas Espaciales

## 1. Introducción al Contexto Espacial

Si todos los tipos celulares de nuestro organismo tienen el mismo material genético, ¿por qué todos tienen funciones diferentes? La sola pregunta ya parece contraintuitiva: ¿cómo pueden diferentes nodos tener los mismos datos de entrada y generar outputs completamente distintos?

A excepción de las células sexuales, las tumorales, los glóbulos rojos y los linfocitos B, todos los billones de células que nos conforman comparten el mismo ADN.

La respuesta no está en qué genes existen en el ADN, sino en cuáles se activan, en qué medida y en qué momento. Durante el desarrollo embrionario, las células reciben instrucciones químicas para especializarse. Sin embargo, estas señales no ocurren al azar; dependen estrictamente de la posición física de la célula dentro del tejido.

Estas señales responden a un contexto espacial: la identidad y función de una célula están determinadas por quiénes son sus vecinas, a qué distancia se encuentran y qué moléculas están intercambiando en un punto específico del espacio $2\text{D}$ o $3\text{D}$.

En cuanto a información biológica, esto significa que no estaremos trabajando con todos los genes, sino únicamente con aquellos que se expresaron: los transcritos de ARN mensajero y en algunos casos, proteínas. El estudio de estas moléculas junto a sus coordenadas geográficas conforma lo que conocemos como **transcriptómica espacial** y **proteómica espacial**.

<img width="6912" height="2304" alt="Flujo de información y Ómicas" src="https://github.com/user-attachments/assets/8b7a3008-cda9-4b05-ab90-efc634a4817b" />

---

## 2. La Abstracción de Datos: Del Tejido a las Estructuras Informáticas

Para un científico de datos o ingeniero de sistemas, un corte de tejido biológico es una fuente de datos no estructurada. La transcriptómica espacial abstrae un tejido físico en tres componentes de datos principales:

### 2.1. La Matriz de Expresión Genómica ($\mathbf{X} \in \mathbb{R}^{N \times M}$)
Representa la abundancia de las moléculas medidas.

* **Filas ($N$ observaciones):** Unidades de muestreo espacial (célula individual, *spot* o área delimitada).
* **Columnas ($M$ características):** Genes o proteínas medidos ($M > 20{,}000$ en transcriptoma completo; $300\text{--}6{,}000$ en paneles dirigidos).
* **Reto de datos:** Matriz de alta dimensión con extrema **dispersión (*sparsity*)** y presencia de ceros técnicos (*dropouts*).

### 2.2. La Matriz de Coordenadas Espaciales ($\mathbf{S} \in \mathbb{R}^{N \times 2}$)
Mapea cada observación $n_i$ en un sistema de coordenadas plano $(x_i, y_i)$ expresado en micrómetros ($\mu\text{m}$) o píxeles:

$$\mathbf{S} = \begin{bmatrix} x_1 & y_1 \\ x_2 & y_2 \\ \vdots & \vdots \\ x_N & y_N \end{bmatrix}$$

Al cruzar $\mathbf{S}$ con la expresión $\mathbf{X}$, la muestra se transforma en un **Grafo Espacial de Vecindad** $G = (V, E)$, donde los nodos $V$ son las células y las aristas $E$ representan relaciones de proximidad física.

### 2.3. El Tensor de Imagen Histológica ($\mathbf{I} \in \mathbb{R}^{H \times W \times C}$)
Capa de contexto morfológico (tinciones H&E o inmunofluorescencia) donde $H \times W$ representan la resolución en píxeles y $C$ los canales de color. Requiere un proceso de **registro de coordenadas** para alinear la matriz $\mathbf{S}$ sobre el tensor $\mathbf{I}$.

### 2.4. El Contenedor Unificado (`AnnData` / `SpatialExperiment`)
Ecosistemas como Python (`Scanpy`/`Squidpy`) y R (`Bioconductor`) estructuran estos componentes en un único objeto de memoria:

```text
Estructura del Objeto AnnData (Spatial)
┌─────────────────────────────────────────────────────────────┐
│ .X                  ────────► Matriz de Expresión (N × M)     │
│ .obs                ────────► Metadata de observaciones       │
│ .var                ────────► Metadata de genes               │
│ .obsm['spatial']    ────────► Coordenadas X, Y (N × 2)        │
│ .uns['spatial']     ────────► Tensor de Imagen H&E + Escalas  │
└─────────────────────────────────────────────────────────────┘
