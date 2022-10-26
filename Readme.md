# TFG - Vision artificial - Medicion de raices de dientes
### Universidad de Burgos - Grado en Ingenieria informatica
### Alvar San Martín 2022/23
---
# 1. Trabajos de referencia
1. [Paper oficial de YOLO](moz-extension://6302a342-024a-461d-8883-79cfac9ebe6b/enhanced-reader.html?openApp&pdf=https%3A%2F%2Farxiv.org%2Fpdf%2F2207.02696.pdf) : Explicacion de su funcionamiento
    - Reconocimiento en video, muy enfocado en tiempo real, irrelevante para la aplicación actual, pero a tener en cuenta a la hora de comparar el tiempo de procesamiento de imagenes.
    - Enfocado al rendimiento en GPUs comerciales
    - Concepto de [reparameritrizacion](#3-sumario)(2.2), posiblemente usado dado que solo necesitamos reconocer una pequeña cantidad de elementos
    - Concepto de [Asignacion dinamica de etiquetas](#3-sumario), 
2. [Articulo de Medium sobre YOLOv4](https://medium.com/visionwizard/yolov4-bag-of-freebies-dc126623fc2d)
    - Concepto de [*Data Augmentation*](#3-sumario)
3. [*Bag of freebies* en YOLOv3](https://arxiv.org/pdf/1902.04103v3.pdf): tecnicas aplicadas a YOLOv3 para obtener una mejora de un 5%

# 2. Posibles modelos pre-entrnados
1. [YOLOv7](https://github.com/WongKinYiu/yolov7): 

# 3. Sumario
1. **Reparametrizacion**: capas, *gradient propagation path*
2. **Asignacion dinamica de etiquetas**: objetivos dinamicos, multiples capas de salida
3. **_Bag of freebies_**: Tecnicas para cambiar la estrategia de entrenamiento y mejorar la precision del modelo, propuestas para la [v4](https://medium.com/visionwizard/yolov4-bag-of-freebies-dc126623fc2d):
    - Data Augmentation
    - Semantic Distribution Bias in Datasets
    - Objective Function of BBox Regression
4. **_Data Augmentation_**: Tecnicas para modificar ligeramente las entradas y que el dataset sea mas variado.