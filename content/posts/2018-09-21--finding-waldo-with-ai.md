---
title: Buscando a Waldo con Deep Learning
date: "2018-09-21T10:40:32.169Z"
template: "post"
draft: false
slug: "finding-waldo-with-ai"
category: "Spanish AI Tutorial"
tags:
  - "Tutorial"
  - "YOLO"
  - "AI"
  - "Image detection"
description: "Tutorial para reconocer placas mediante inteligencia artificial"
socialImage: "https://media-exp1.licdn.com/dms/image/C5612AQGIlCs485VfeA/article-cover_image-shrink_600_2000/0?e=1587600000&v=beta&t=OVpDTxmesVpY1bXQ53hAcxmaCtTG3r1CMz4eiHJ-c4s"
---
### Visión computacional y deep learning
El campo de la visión computacional es un gran beneficiario del deep learnig, ya que los método basados en esta rama de la inteligencia artificial han superado los métodos estadísticos clásicos llegando a superar de manera sustancial a los métodos tradicionales.


<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQFOGzAgjkRJxg/article-inline_image-shrink_1000_1488/0?e=1587600000&v=beta&t=tFrMyRC4RzeNPVU4GcVM4px-0UvVcIgmLQzxhAz4qKs">
	<figcaption>http://www.cs.toronto.edu/~urtasun/courses/CSC2541_Winter17/detection.pdf</figcaption>
</figure>

Existen algunos algoritmos en la actualidad desempeñando una competencia de precisión, por lo cual actualmente estos son los principales:

* You Only Look Once (YOLO)
* Single Shot Detector (SSD)
* Faster Region CNN (F R-CNN)

### Darknet YOLO
Para este ejemplo se usará YOLO que presenta una gran capacidad predicativa y rapidez en las predicciones catalogado para uso en tiempo real.

<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQEKG7JkD0hkiQ/article-inline_image-shrink_1000_1488/0?e=1587600000&v=beta&t=lWc9Ko7T5_XF3ercR6Rwh4FH2270LdjanTifi8z3eDw">
	<figcaption>https://pjreddie.com/darknet/yolo/ </figcaption>
</figure>


Se enseñara al algoritmo de detección de objetos a buscar a Waldo mediante ejemplos obtenidos de Google Images y entrenando el algoritmo de YOLO en su versión 2 y 3. Para esto es necesario tener las librerías darknet para ejecutar la red neuronal convolucional. El código fuente del framework darknet se lo encuentra en la siguiente dirección:

(https://github.com/pjreddie/darknet)

Se compila para el sistema operativo respectivo, adicionalmente tener en cuenta el poder las GPU, un entrenamiento sin estas puede demorar hasta semanas dependiendo de la cantidad de clases e imágenes es por eso que es muy recomendable tener GPU con CUDA. Para este ejemplo se usa nodos en Linux con 4 GPU Tesla K80.

### Manos a la obra
Inicialmente se procede a clonar el repositorio con el código de este articulo, con los scripts para aumentar el conjunto de imágenes mediante transformaciones y generar los archivos de configuración para la red YOLO v2 y v3:

(https://github.com/FabianBG/finding_waldo_yolo)

En este repositorio se encuentran los siguientes scripts e imágenes usadas para el entrenamiento del buscador de Waldo:

* image_augmentation.py: Script en Python3 para aumentar el tamaño del conjunto inicial de imágenes. Basado en imgaug, si se quiere hacer uso de este script instalar el paquete (https://imgaug.readthedocs.io/en/latest/source/installation.html)
* proccess_dataset.py: Script en Python3 que genera los conjuntos de pruebas y entrenamiento para la red.
* waldo.data: Archivo con la meta-data de la red a entrenar.
* waldo.names: Archivo con los nombres de las clases
* yolov2-waldo.cfg: Configuración de la red YOLOv2.
* yolov3-waldo.cfg: Configuración con la red YOLOv3.
* data/: Carpeta con las imágenes de Waldo, para este ejemplo se trabajará solo con imagenes *.jpg en caso de agregar mas mantener el formato.
* data/aug: Carpeta de salida del script de aumentación de imágenes.
* validate/ : Carpeta con imágenes de validación del modelo.
* test/: Imágenes para pruebas

Lo primero que necesitamos son los pesos de la red pre-entrenados:

* YOLOv2 (https://pjreddie.com/media/files/darknet19_448.conv.23)
* YOLOv3 (https://pjreddie.com/media/files/darknet53.conv.74)

El segundo paso es opcional ya que consiste en generar mas imágenes mediante transformaciones, para esto se ejecuta la siguiente linea:

```
# finding_waldo_yolo/
python ./image_augmentation.py
```

Lo que genera las imágenes en la carpeta aug dentro de data. Si se desea aplicar diferentes transformaciones referirse al manual de imgaug (https://imgaug.readthedocs.io/en/latest/)

<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C5612AQHzTPpakOttLg/article-inline_image-shrink_1500_2232/0?e=1587600000&v=beta&t=p6d15ZTMEcRnbC-goUwD9_ZVOcWvCpS4ljx3ChcoKb0">
</figure>

Ahora se configura las conjuntos de entrenamiento:

```
# finding_waldo_yolo/
python ./proccess_dataset.py [images_location_dir] [darknet_search_path]
```

Lo que generará dos archivos de texto train.txt y test.txt que contienen las rutas de las imágenes separadas en conjuntos de entrenamiento y pruebas.

<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQE-hflEymoGoA/article-inline_image-shrink_1000_1488/0?e=1587600000&v=beta&t=TVlTZbtPk-gZMw4m6GlFxwvjA72NhMneIyP3THSsMek">
</figure>

Bien ahora se tiene los datos, se realiza la configuración e la meta-data de la red mediante al archivo waldo.data

```
# finding_waldo_yolo/waldo.data

classes = 1 # Número de clases
train  = ./train.txt # Path de el archivo train.txt
valid  = ./test.txt # Path de el archivo test.txt
names = ./waldo.names # Path del archivo waldo.names
backup = ./backup/ # Directorio donde se almacenaran los pesos del entrenamiento
```

Finalmente se empieza el entrenamiento ejecutando el siguiente comando:

```
# finding_waldo_yolo
# YOLOv3
darknet detector train ./waldo.data ./yolov3-waldo.cfg ./darknet53.conv.74

# YOLOv2
darknet detector train ./waldo.data ./yolov2-waldo.cfg ./darknet19_448.conv.23
```

Cada versión imprime un salida diferente, pero cada una imprime una salida después da cada epoch iteración similar, en este caso se filtro la salida para solo mostrar el resultado de cada iteración mediante 

`[comando] | grep images:`

<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQGKtmiZHOOP_w/article-inline_image-shrink_1500_2232/0?e=1587600000&v=beta&t=BtmYq2hnAX-K22921Ddjiu2K_KycXYv57d4F1k2Suts">
</figure>

```
# Salida de entrenamiento

11203: 0.097249, 0.100165 avg, 0.004000 rate, 25.083207 seconds, 2867968 images
```

En donde los 3 primeros números especifican:

* 11203 Numero de iteración
* 0.097249 Perdida general
* 0.100165 Perdida promedio

Entre menor la perdida mayor precisión, pero tener en cuenta posibles sobre ajustes de la red por esto probar con diferentes pesos generados automáticamente, YOLO genera por defecto pesos para 400, 800 y de ahí en adelante de 10000 en 10000 y el archivo de backup donde esta en la iteración actual. Con esto es posible detener el entrenamiento y empezarlo nuevamente haciendo uso de este archivo de backup.

<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C5612AQHpRETDOJX6xg/article-inline_image-shrink_1000_1488/0?e=1587600000&v=beta&t=QMVuFBKSdhYuYk9XDfhPlqmSEEtDTWm3ZD0seFbDDTU">
</figure>

Se obtendrá una serie de archivos de pesos con los cuales podremos probar cuales son los mejores y mas estables sin sobre ajuste mediante pruebas con las imágenes en a carpeta de validate/ para probar los pesos simplemente se ejecuta el siguiente comando:

```
# Darknet instalation folder
./darknet detector test cfg/waldo.data \ 
                        cfg/yolov2-waldo.cfg \
                        [path_weights]/yolov2-waldo_10000.weights \
                        [path_finding_waldo_yolo]/validate/1.jpg

```
<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C5612AQFJKCG1jKS7dQ/article-inline_image-shrink_1000_1488/0?e=1587600000&v=beta&t=hHuZfg1OJ5ACe_IwPM8JIUAxy2jQ3mtYiosnHF-piSo">
</figure>

Se mostrará en una ventana la imagen con la predicción, mediante esta prueba se puede saber que pesos son los mas adecuados para generalizar mejor la búsqueda. Peor existe un problema YOLO no tiene mucha capacidad predictiva sobre objetos pequeños ya que se re dimensiona la imagen de entrada a predecir por lo que se pierden detalles en imágenes sumamente grandes por lo que se genero un script que genera una ventana de recorrido y genera predicciones por cada ventana mostrando los resultados finales.
```

# finding_waldo.py
import os
import sys
import cv2 as cv
import python.darknet as dn
import pdb

sys.path.append(os.path.join('/home/mbastidas/git/darknet/python/'))

weigths = sys.argv[2]
config = sys.argv[3]
file_dir = sys.argv[1]
parts = int(sys.argv[4])

def convert_axis(size, box):
    dw = 1./size[0]
    dh = 1./size[1]
    x = box[0]/dw
    w = box[2]/dw
    y = box[1]/dh
    h = box[3]/dh

    b1 = x + (w/2)
    b3 = y + (h/2)
    b0 = x + (w/2) - w
    b2 = y + (h/2) - h

    return (b0, b1, b2, b3)

def divide_image(file_dir, parts):
    padding = 50
    names = []
    image = cv.imread(file_dir)
    shape = image.shape
    partes = int(int(parts / 2))
    print("Dividiendo imagen", file_dir, shape, "en", partes*2)
    for i in range(0, partes):
        w = int(shape[1] / partes)
        x = int(i * w)
        w = x + w + padding
        for j in range(0, partes):
            h = int(shape[0] / partes)
            y = int(j * h)
            h = y + h + padding
            print(x, w, y, h)
            if i == partes and j == partes:
                part = image[y:shape[0], x:shape[1]]
            else:
                part = image[y:h, x:w]
            name = "./" + str(x) + "_" + str(y) + ".jpg"
            cv.imwrite(name, part)
            names.append(name)
    return image, names, (int(shape[1] / partes), int(shape[0] / partes))

def draw_box(image, points, part_shape, acc, color=(0, 0, 255)):
      x = int(points[0] + part_shape[0] - (points[2]/2))
      y = int(points[1] + part_shape[1] - (points[3]/2))
      x1 = int(points[2] + x)
      y1 = int(points[3] + y)
      print((x,y), (x1,y1))
      cv.rectangle(image, (x,y), (x1,y - 25), color, -1)
      cv.putText(image,"%.2f" % round(acc,2),(x,y-15), cv.FONT_HERSHEY_SIMPLEX,
      1,(255,255,255),2,cv.LINE_AA)
      cv.rectangle(image, (x,y), (x1,y1), color, 3)
      print("save on find_waldo_" + file_dir.split("/")[-1])
      cv.imwrite("./find_waldo_" + file_dir.split("/")[-1], image)


image, names, part_shape = divide_image(file_dir, parts)
print(names)
net = dn.load_net(config.encode('utf-8'), weigths.encode('utf-8'), 0)
meta = dn.load_meta("cfg/waldo.data".encode('utf-8'))
insight = None
boxes = []
part = None
for name in names:
    #[class, prob, (x,y, w,h)]
    predictions = dn.detect(net, meta, name.encode('utf-8'))
    print("Parte", name)
    os.remove(name)
    print("Pred", str(len(predictions)))
    if len(predictions) > 0:
        if insight == None:
            insight = predictions[0] 
            part = name[2:].split(".")[0].split("_")
        for pred in predictions:
            print(pred)
            boxes.append({
            "part": name[2:].split(".")[0].split("_"),
            "insight": pred
            })
            if pred[1] > insight[1]:
                insight = pred
                part = name[2:].split(".")[0].split("_")
print("Best result", insight, part_shape)
if insight != None:
    for box in boxes:
        draw_box(image, box["insight"][2], (int(box["part"][0]), int(box["part"][1])),
        box["insight"][1], color=(255, 0, 0))
    draw_box(image, insight[2], (int(part[0]), int(part[1])), insight[2])
else:
    print("No se encontro nada.")

```    

El script debe estar sobre la carpeta de instalación de darknet ya que hace uso de el wrapper de python para darknet. Este corta una imagen en 4 u 8 secciones y las analiza una por una, graficando todas las predicciones en azul y la mas alta con verde. Este script es especialmente eficaz en imágenes grandes.

### Pruebas finales
Se ejecuta el siguiente comando para ejecutar el script anterior sobre las imágenes en la carpeta de validación

```
# Darknet folder
#! bin/sh
for filename in /home/mbastidas/git/yolo-models/waldo/validate/*
do
        python find_waldo.py $filename results/yolov3-waldo_10000.weights cfg/yolov3-waldo.cfg 4
done
```

Este comando ejecuta el script de python de segmentación y genera un imagen con los resultados marcando con verde la mejor predicción y con azul otras predicciones, cada archivo generado se marca con el prefijo 

`find_waldo_[nombre_imagen_validacion].jpg`

El resultado se lo puede ver por YOUTUBE (https://www.youtube.com/watch?v=6NKX1Bk8TJg)

### Conclusiones
YOLO es una forma relativamente simple de generar detectores de objectos con una buena tasa de precisión y de forma rápida (**GPU required**). YOLOv2 es mas rápido pero tiende a tener menor precisión al contrario que YOLOv3 que presenta una mejoría de precisión pero a su ver aumenta la complejidad de la red lo cual aumenta los tiempos de predicción y entrenamiento.

<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQG1p9JlHLE2Vw/article-inline_image-shrink_1500_2232/0?e=1587600000&v=beta&t=PNcntOUAe7-qRwy36bzf0iB-Nq1HAnTZR06fTe_5GVA">
</figure>

Existen casos en los que que erra completamente en la búsqueda porque generalmente para el entrenamiento de algoritmos de deep learning siempre es necesario grandes cantidades de datos, entre mas imágenes se tenga mejor sera la precisión del mismo, como mejoría la trabajo actual se propone aumentar el conjunto de imágenes y probar con mas datos.

Finalmente no solo YOLO existe actualmente, hay algoritmos que están compitiendo constantemente por mejorar su tasa de precisión por lo que vale estar al día en la evolución e las arquitecturas y mejoras de todos.