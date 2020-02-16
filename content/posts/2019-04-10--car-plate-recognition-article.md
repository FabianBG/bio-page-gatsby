---
title: Reconocimiento de placas con inteligencia artificial
date: "2019-04-10T10:40:32.169Z"
template: "post"
draft: false
slug: "plate-recognition-tutorial"
category: "Spanish AI Tutorial"
tags:
  - "Tutorial"
  - "Tensorflow Keras"
  - "AI"
  - "Image detection"
description: "Tutorial para reconocer placas mediante inteligencia artificial"
socialImage: "https://media-exp1.licdn.com/dms/image/C4E12AQE8mRZaamYaWQ/article-cover_image-shrink_720_1280/0?e=1587600000&v=beta&t=xzDzkvKlrdeUh19RQMxvNYx7tPffSRcMzhHWdmbzZZw"
---
La inteligencia artificial permite actuar sobre muchos campos empresariales, y automatizar de manera eficaz procesos de negocio, en este articulo se propone una tarea simple de extracción de texto de una imagen similar a una placa mediante la aplicación de redes convolucionales y recurrentes que basándose en una imagen de entrada predice el texto en la misma. Para esto es necesario tener un conjunto de datos, que dentro del campo del deep learning es la materia prima para poder generar modelos precisos.

Todo el código de este articulo se lo puede encontrar en el repositorio: (https://github.com/FabianBG/plate_recognition)

### Conjunto de datos
Como principal materia prima para el modelo que se entrenará, se procede con un generador de imágenes que cumplirá la función de crear un conjunto de imágenes al azar, para esto en nuestro código definimos un generador que cree imágenes basados en el formato de una placa de automóvil (No seremos estrictos con la validación de las mismas).

``` 
# Python3
# plate_recognition/src/ocr_plate.py

def random_plate():
	    size = randint(plate_min, plate_max)
	    letter = ""
	    number = ""
	    for _ in range(size):
	        letter = letter +
              chr(big_letters[randint(0, len(big_letters) - 1)])
	        number = number + chr(digits[randint(0, len(digits) - 1)])
	    return letter + "-" + number

def get_plate(size, empty=False):
	    plate = random_plate()
	    font = ImageFont.truetype(font_path, 30)
	    img = Image.new('RGB', size, (0,0,0))
	    draw = ImageDraw.Draw(img)
	    rounded_rectangle(draw, ((0, 0), size), 20,
            fill=(255, 255, 255), outline=(255, 255, 255))
	    draw.text((25,10), plate, font=font, fill=(0,0,0))
	    img = np.array(img.convert('L'))
	    img = img.astype(np.float32) / 255
	    img = np.expand_dims(img, 0)
	    return img, plate
	

	

	

	def image_generator(size, img_w, img_h, downsample_factor):
	    if K.image_data_format() == 'channels_first':
	        X_data = np.ones([size, 1, img_w, img_h])
	    else:
	        X_data = np.ones([size, img_w, img_h, 1])
	    labels = np.ones([size, absolute_max_string_len])
	    input_length = np.zeros([size, 1])
	    label_length = np.zeros([size, 1])
	    source_str = []
	    for i in range(size):
	        data, world = get_plate(image_size, True)
	        if K.image_data_format() == 'channels_first':
	            X_data[i, 0, 0:img_w, :] = np.array(data).T
	        else:
	            X_data[i, 0:img_w, :, 0] = data[0, :, :].T
	        labels[i] = np.ones(absolute_max_string_len) * -1
	        labels[i, 0:len(world)] = text_to_labels(world)
	        input_length[i] = img_w // downsample_factor - 2
	        label_length[i] = [len(world)]
	        source_str.append(world)
	    inputs = {'input': X_data,
	                'labels': labels,
	                'input_length': input_length,
	                'label_length': label_length,
	                'source_str': source_str
	                }
	    outputs = {'ctc': np.zeros([size])}
	    return (inputs, outputs)
```

En el fragmento de código anterior se muestra las funciones principales para generar conjuntos de imágenes. Principalmente se obtiene una cadena de texto con el texto de la placa a generar esto mediante la función **random_plate()** esta función proporciona al azar una serie de 3 o 4 letras separadas por un guion y otra serie de 3 o 4 números.

Una vez proporcionado el texto a dibujar la función **get_plate()** se encarga de pintar una imagen, que a su vez es usada por la función **image_generator()** que se encarga de modelar la estructura de datos para nuestro modelo.

La estructura de entrada consta de 4 elementos:

* input: Que es la imagen transformada a un vector de datos normalizado entre 1 y 0.
* labels: Que es el texto de la imagen pero transformado a un formato de vector para que sea la salida   esperada de la red neuronal.
* input y label length: Que es el tamaño de cada vector de datos.
* source_str: Que es la cadena de texto con el texto original de la imagen.

Al final se define un generador que constantemente esta alimentando el modelo:

```
# Python3
# plate_recognition/src/ocr_plate.py

def batch_generator(size):while 1:
        data = image_generator(size, img_w=200, img_h=50, downsample_factor=4)
        yield data

```
Este recibe un tamaño de conjunto para generar el mismo.

### Modelo
Una lista la materia prima del modelo, se genera un modelo compuesto principalmente de dos tipos de capas:

* Convolucionales, que son encargadas de extraer características de una imagen.
* Recurrentes, que por medio de una implementación de memoria temporal son capaces de predecir cadenas secuenciales de datos.

El modelo es generado mediante el siguiente fragmento de codigo:

```
# Python3
# plate_recognition/src/ocr_plate.py

def model(input_shape, output_shape, absolute_max_string_len):# Input Parameters
    img_h = input_shape[1]
    img_w = input_shape[0]
    
    # Network parameters
    conv_filters = 16
    kernel_size = (3, 3)
    pool_size = 2
    time_dense_size = 32
    rnn_size = 512


    if K.image_data_format() == 'channels_first':
        input_shape = (1, img_w, img_h)
    else:
        input_shape = (img_w, img_h, 1)


    act = 'relu'
    input_data = Input(name='input', shape=input_shape, dtype='float32')
    inner = Conv2D(conv_filters, kernel_size, padding='same',
                   activation=act, kernel_initializer='he_normal',
                   name='conv1')(input_data)
    inner = MaxPooling2D(pool_size=(pool_size, pool_size), name='max1')(inner)
    inner = Conv2D(conv_filters, kernel_size, padding='same',
                   activation=act, kernel_initializer='he_normal',
                   name='conv2')(inner)
    inner = MaxPooling2D(pool_size=(pool_size, pool_size), name='max2')(inner)


    conv_to_rnn_dims = (img_w // (pool_size ** 2),
                        (img_h // (pool_size ** 2)) * conv_filters)
    inner = Reshape(target_shape=conv_to_rnn_dims, name='reshape')(inner)
    inner = Dense(time_dense_size, activation=act, name='dense1')(inner)
    gru_1 = GRU(rnn_size, return_sequences=True,
                kernel_initializer='he_normal', name='gru1')(inner)
    gru_1b = GRU(rnn_size, return_sequences=True,
                 go_backwards=True, kernel_initializer='he_normal',
                 name='gru1_b')(inner)
    gru1_merged = add([gru_1, gru_1b])
    gru_2 = GRU(rnn_size, return_sequences=True,
                kernel_initializer='he_normal', name='gru2')(gru1_merged)
    gru_2b = GRU(rnn_size, return_sequences=True, go_backwards=True,
                 kernel_initializer='he_normal', name='gru2_b')(gru1_merged)
    inner = Dense(output_shape, kernel_initializer='he_normal',
                  name='dense2')(concatenate([gru_2, gru_2b]))
    
    y_pred = Activation('softmax', name='softmax')(inner)
    Model(inputs=input_data, outputs=y_pred).summary()


    labels = Input(name='labels',
                   shape=[absolute_max_string_len], dtype='float32')
    input_length = Input(name='input_length', shape=[1], dtype='int64')
    label_length = Input(name='label_length', shape=[1], dtype='int64')
    loss_out = Lambda(
        ctc_lambda_func, output_shape=(1,),
        name='ctc')([y_pred, labels, input_length, label_length])
    test_func = K.function([input_data], [y_pred])
    sgd = SGD(lr=0.02, decay=1e-6, momentum=0.9, nesterov=True, clipnorm=5)
    model = Model(inputs=[input_data, labels, input_length, label_length],
                  outputs=[loss_out, y_pred])
    model.compile(loss={'ctc': ctc, 'ctc': ctc}, optimizer=sgd)


    return model, test_func
```

Que de forma más simplificada consta del siguiente resumen:

```
# model.summary()

TensorFlow version: 1.12.0
Keras version: 2.2.4
__________________________________________________________________________________________________
Layer (type)                    Output Shape         Param #     Connected to                     
==================================================================================================
input (InputLayer)              (None, 200, 50, 1)   0                                            
__________________________________________________________________________________________________
conv1 (Conv2D)                  (None, 200, 50, 16)  160         input[0][0]                      
__________________________________________________________________________________________________
max1 (MaxPooling2D)             (None, 100, 25, 16)  0           conv1[0][0]                      
__________________________________________________________________________________________________
conv2 (Conv2D)                  (None, 100, 25, 16)  2320        max1[0][0]                       
__________________________________________________________________________________________________
max2 (MaxPooling2D)             (None, 50, 12, 16)   0           conv2[0][0]                      
__________________________________________________________________________________________________
reshape (Reshape)               (None, 50, 192)      0           max2[0][0]                       
__________________________________________________________________________________________________
dense1 (Dense)                  (None, 50, 32)       6176        reshape[0][0]                    
__________________________________________________________________________________________________
gru1 (GRU)                      (None, 50, 512)      837120      dense1[0][0]                     
__________________________________________________________________________________________________
gru1_b (GRU)                    (None, 50, 512)      837120      dense1[0][0]                     
__________________________________________________________________________________________________
add_1 (Add)                     (None, 50, 512)      0           gru1[0][0]                       
                                                                 gru1_b[0][0]                     
__________________________________________________________________________________________________
gru2 (GRU)                      (None, 50, 512)      1574400     add_1[0][0]                      
__________________________________________________________________________________________________
gru2_b (GRU)                    (None, 50, 512)      1574400     add_1[0][0]                      
__________________________________________________________________________________________________
concatenate_1 (Concatenate)     (None, 50, 1024)     0           gru2[0][0]                       
                                                                 gru2_b[0][0]                     
__________________________________________________________________________________________________
dense2 (Dense)                  (None, 50, 38)       38950       concatenate_1[0][0]              
__________________________________________________________________________________________________
softmax (Activation)            (None, 50, 38)       0           dense2[0][0]                     
==================================================================================================
Total params: 4,870,646
Trainable params: 4,870,646
Non-trainable params: 0
__________________________________________________________________________________________________
```

El modelo hace uso de la implementación de redes recurrentes GRU, que en este caso resultan más eficientes que las LSTM.

En resumen el modelo recibe una imagen de entrada y mediante las capas de convolución extrae las principales características que a su vez son la entrada de las capas recurrentes las cuales generan las predicciones basándose en un máximo tamaño permitido.

### Función de optimización
Dentro de cualquier problema de deep learning tiene que existir un objetivo o función a optimizar, ya sea el caso de una regresión que busca predecir numéricamente un valor o una clasificación que pretende predecir una clase, lo que se busca es optimizar una función, para este caso en particular lo que se busca es que la salida que es una matriz la cual consta de 2 dimensiones, en la que la primera es el tamaño máximo de la cadena que puede predecir el modelo y la segunda el alfabeto.

En la siguiente imagen se puede apreciar una representación simple de la matriz de salida.

<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQH04kcWT-WgHg/article-inline_image-shrink_1000_1488/0?e=1587600000&v=beta&t=UFRHUOCDUQGLPgRX0qywCt3ytjnsA4kM-W4zqvJ2gWk">
</figure>


Para poder optimizar esta matriz y decidir el camino correcto en que los pesos del modelo se ajustan las funciones de perdida comúnmente conocidas no ayudan, pero para este caso en particular se usa una función de perdida especial conocida como CTC(Connectionism Temporal Classification) que en resumen se encarga de definir una especie de camino desde la predicción inicial hasta la final.

### Entrenamiento
El entrenamiento del modelo se lo realizó por 20 ciclos o epochs con un batch_size de 64, sobre un equipo que cuenta con tarjeta grafica NVIDIA 1060, aproximadamente el entrenamiento duro 15 a 20 minutos y el modelo se entreno hasta que el error se redujo a menos de 1.

Para iniciar el entrenamiento del modelo simplemente iniciar dentro del directorio del código:

```
# Python3

python3 main.py train

```
<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQE7Eg0K1VR01A/article-inline_image-shrink_1000_1488/0?e=1587600000&v=beta&t=syiJB5QpkeKlcZ99QOUTC8Ut2wPp7NC5ZmScgqg48ZI">
</figure>


### Pruebas
Una vez se entrenaron los modelos, estos se guardan automáticamente cada 2 epochs, en el mismo directorio del código fuente.

<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQErvABpFlU_Rw/article-inline_image-shrink_1000_1488/0?e=1587600000&v=beta&t=RtDc9wVNH35-DXDzn5TeVAIzHsnL_Lk0UXom4wl0Ea0">
</figure>
Para las pruebas usaremos estos modelos, para poder ejecutar una prueba ejecutar el siguiente comando:

```
# Python3

# python3 main.py [numero_epoch]

python3 main.py 06
```
<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQHeTQFbdmocOw/article-inline_image-shrink_1000_1488/0?e=1587600000&v=beta&t=GkV4GgbipdBa6vjzwZf5rL7x2NgMu7nC2cPCH563GXo">
</figure>
<figure>
	<img src="https://media-exp1.licdn.com/dms/image/C4E12AQE624rhwaMQqw/article-inline_image-shrink_1000_1488/0?e=1587600000&v=beta&t=hT3NEY773bUd4cxpnlwDtul5V-v3l_suWm_BWnSApck">
</figure>


### Conclusiones
De manera rápida y simple se puede generar un modelo que permita automatizar un proceso y generar valar a una empresa, y gracias a las herramientas actuales muchas partes de la programación están abstraídas y permiten un rápido desarrollo.

El modelo es una demostración simple ya que para que sea un modelo final que sea viable se tiene que mejorar el conjunto de datos y la variedad de ejemplos ya que como se sabe en el mundo real no todo esta estático y perfectamente retratado.

### Referencias
Keras OCR Sample (https://keras.io/examples/image_ocr/)