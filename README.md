Проект EyeBird (в стадии разработки)

EyeBird - нейронная SSD-Like сеть ,написанная на TensorFlow (переписана с оригинального framework Caffe)

Цель EyeBird - быстрое и корректное обнаружение автомобилей (седан,хеджбек,пикап,трак/автобус) в видеопотоке с возможностью обработки
до 46 fps

В качестве обучающий выборки было принято решение выбрать COWC Dataset 
https://gdo152.llnl.gov/cowc/mundhenk_et_al_eccv_2016.pdf

На момент осени 2018 года самый всеобъемлющий датасет с аннотациями.
Подробнее про архитектуру SSD вы можете найти здесь: https://arxiv.org/pdf/1512.02325.pdf

Этот тред подразумевает ,что читатель уже имеет представление о тот что такое CNN,SSD и знаком с TensorFlow
Мы будем рассматривать лишь те изменения , которые будем вносить в параметры классической архитектуры SSD сети.Например,
как корректно рассчитать {aspect_ratios} под собственный датасет и какие {size} устанавливать в качестве глобальных гиперпараметров.
Все эти детали понадобятся для дальнейшего улучшения mAP ,так как объекты в обучающем датасете это спутниковые снимки и размер целевых объектов (автомобилей)
гораздо меньше целевых объектов в таких датасетах как PASCAL VOC или же COCO
Вообще идея с самостоятельным расчетом гиперпараметров {aspect_ratios} и {size} лишь гипотеза ,которую стоит проверить уже на практике сравнив fps и mAP сети.


![Image alt](https://github.com/toseek01/eyebird/blob/master/illustrator/highway_edit.png)

SSD Network - это набор взаимосвязанных файлов , где каждый файл отвечает за свое поле работы , начиная с Data augmentation
и заканчивая Anchor Boxes Decoding
Ключевой файл в проекте [model_function.py](https://github.com/toseek01/eyebird/blob/master/model_function.py)
Файл описывает обучающую архитектуру сети.На вход подаются изображения 300х300 пикселей.Для извлечение features используется архитектура 
VGG16 
![Image alt](https://github.com/toseek01/eyebird/blob/master/illustrator/ssd_arch.png)

Только последние full connected layers заменяются на convolutional layers и далее добавляются еще дополнительные convolutional layers.Так же для уменьшения затрат в сверточных слоях задействуются буферные [1х1 свертки](https://stats.stackexchange.com/questions/194142/what-does-1x1-convolution-mean-in-a-neural-network)

Для упрощения обучения сети можно воспользоваться [Transfer Learning](https://towardsdatascience.com/transfer-learning-in-tensorflow-9e4f7eae3bb4) и предобученной VGG-16 c уже посчитанными весами. Для этого можно использовать уже готовые библиотеки [tensornets](https://github.com/taehoonlee/tensornets) или же [vgg-tensorflow](https://github.com/machrisaa/tensorflow-vgg).
Единственное что надо будет изменить в config file ,чтобы сеть отдавала не full connected layer,а сверточный слой conv4_3 ,так как он учавствует в обучение (классификация объекта и его локация)

