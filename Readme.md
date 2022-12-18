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

# 4. Limpieza y carga de datos

Para comenzar es necesario seleccionar que datos (imagenes + segmentacion) vamos a usar para entrenar el modelo.

En el trabajo original es set de datos es de 10 radiografias validas, lo que es una tamaño extremadamente reducido. Inicialmente se convierten las anotaciones de los segmentos a archivos json individuales, lo que no es demasiado optimo teniendo en cuenta que se generan 2 por imagen, uno para el diente y el otro para la raiz, aparentemente se pasan a formato COCO, pero en los noteboooks aparentemente faltan partes y el json COCO nunca se almacena en disco sino que se genera en memoria y despues se ignora. [No se razona en nuingun momento esta decision]

En nuestro trabajo se utiliza un dataset de 53 imagenes, sin embargo no todas contienen las radiografias completas, por lo que primero hay que seleccionar las que:
- Esten contenidas en directorios sin un "NO" en el nombre.
- Las que contengan un archivo json para la segmentacion del diente y otro para la raiz.

Los demas se barajara utilizarlos para mejorar el modelo en el futuro, ya que contienen datos utiles, aunque incompletos.

A la hora de generar el dataset en nuestro caso se va a convertir y almacenar en foramto COCO, de esta forma no sera necesario hacerlo en cada ejecucion. Pasamos de un directorio tal que:

- Origen/
    - CASO 1/
        - 1A.jpg
        - 1B.jpg
        - CASO 1 ROOT CANAL.json
        - CASO 1 THOOT.json
    - CASO 2/
        - 2a.jpg
        - 2b.jpg
        - CASO2 ROOT CANAL.json
        - CASO2 THOOT.json
    - CASO 3NO/
        - 3A.jpg
        - 3b.jpg
        - CASO3 THOOT.json

Como en el ejemplo, en el set de datos hay nombres que no siguen la estructura que deberian y carpetas a las que les faltan archivos.

El dataset generado a partir de este, de acuerdo con el estandard de COCO, tendra esta forma:

- Origen/
    - data/
        - 1A.jpg
        - 10a.jpg
        - 20 Y 21.jpg
        - ...
    - labels.json
    - labels_init.json

Se mantienen las diferencias en los nombres de archivo, pero puesto que en labels.json, donde se almacena la segmentacion de *todas* las imagenes en *data* se identifican por un id unico y no por nombre no hay ningún problema.

*labels.json* contiene la estructura del formato COCO, con los grupos principales y los metadatos, se utilizara para copiarlo en el labels definitivo. Este archivo nunca se modificara de forma automatica, solo a mano si es nesario modificar algo para todos los datos.

# 5. Preparacion de los sets de datos

### Data augmentation
---
En el trabajo original se realiza un proceso que consiste en rotaciones de las imagenes de dsitintas formas, lo que se puede considerar insuficiente, puesto que no se tiene en cuenta el contraste, iluminacion, escala o ruido y unicamente se generan 3 imagenes nuevas por cada original.

En el trabajo nuevo se plantea el uso del metodo de [Keras](https://www.educba.com/keras-data-augmentation/) que de forma automatica nos va a servir para generar un dataset mas completo, con al menos 5 imagenes nuevas por cada una original, que nos dejará con 150 imagenes. Los metodos seleccionados son:

- Rotacion
- Escalado
- Contraste
- Ruido
- Negativo

Ademas se podra añadir:

- Espejo
- Omnision de partes de la imagen
- Recorte
- Enfoque/Desenfoque

### Antes de comenzar el proceso
---
La situacion ideal seria poder utilizar validacion cruzada de datos, no exhaustiva, en varios sub-sets, sin embargo se cuenta solo con unas pocas imagenes lo que dificulta la tarea. Alternativamente se plantean estas posibilidades:

- Crear sets con imagenes que no estan en el set original para validacion.
- Desechar la validacion cruzada y usar 3 sets de entrenamiento, pruebas y validacion.
- Usar las imagenes obtenidas del proceso de "*data augmentation*" en test, lo que esta desaconsejado.

