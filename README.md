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


![Image alt](https://github.com/toseek01/eyebird/blob/master/illustrator/highway_edit.png)

SSD Network - это набор взаимосвязанных файлов , где каждый файл отвечает за свое поле работы , начиная с Data augmentation
и заканчивая Anchor Boxes Decoding
Ниже мы поговорим о model_function.py