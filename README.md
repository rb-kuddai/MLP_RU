# Описание 
[Machine Learning Practical](http://www.inf.ed.ac.uk/teaching/courses/mlp/) - этот курс был посвящен нейронным сетям. В нем мы постепенно реализовывали небольшой фреймворк для нейронных сетей на numpy (разрешалось использовать **только Python и Cython**), которые попадают в категорию Sequential Neural Network (благо это большинство на настоящий момент). Курс состоял из двух больших заданий. Оба задания, оценивались по успешности классификации цифр из MNIST. Отчет о коде, который непосредственно писал я представлен в виде jupyter notebook. 

В первом задании ([03_MLP_Coursework1.ipynb](https://github.com/rb-kuddai/MLP_RU/blob/master/03_MLP_Coursework1.ipynb)) требовалась реализовать обычный Multiple Layer Perceptron. Я получил 90/100. 

Второе задание ([s1569105-07_MLP_Coursework2.ipynb](https://github.com/rb-kuddai/MLP_RU/blob/master/s1569105-07_MLP_Coursework2.ipynb)) было интересней и посвящено регуляризации, Autoencoder'ам и Convolutional Neural Networks. Основная проблема была в том, что наивная реализация оператора свертки через циклы была слишком медленной на Python. Cython реализация делала скорость терпимой, но все равно недостаточной. Лучшие результаты получились при преобразовании операций свертки в умножении двухмерных матриц. Это, в зависимости от того как был установлен numpy, позволяло задействовать низкоуровневую библиотеку OpenBLAS и несколько ядер. Моя реализация [conv.py](https://github.com/rb-kuddai/MLP_RU/blob/master/mlpractical/mlp/conv.py). Файл на самом деле небольшой и большую часть занимают doctests. Отладка была осложнена, тем что сложно было отличить связанны ли плохие результаты при классификации с плохими макропараметрами или реализаций. Сильно помогли метод конечных разностей для проверки градиентов и небольшой тест для проверки сверток, который я подчерпнул из этой статьи: 
> Chellapilla, Kumar, Sidd Puri, and Patrice Simard.  "High performance convolutional neural networks for document processing."  Tenth International Workshop on Frontiers in Handwriting Recognition. Suvisoft, 2006 

Финальном штрихом в производительности было, то что я заметил небольшой недостаток в коде, который предоставлялся нам. Реализация контейнера для различных слоев нейронов считала ошибку (backpropagation) в том числе и для самого первого скрытого слоя (тот что следует сразу после слоя с входным изображением). В виду ограничений железа, наши сверточные сети были весьма неглубокими, и большинство нагрузки приходилось на первый скрытый слой, т.к. на него приходил самый большой тензор. Поэтому, когда я устранил этот недостаток, то производительность увеличилась почти вдвое. Исправление можно увидеть в [layers.py](https://github.com/rb-kuddai/MLP_RU/blob/master/mlpractical/mlp/layers.py) в классе MLP_fast, строка 194. В купе с аугментированными данными это позволило достичь 99.21% на тестовом датасете (10000 изображений) и получить 100/100 за задание. 

P.S. Одной из частей заданий было предобучение сети с помощью и autoencoder'ов. Ни у кого из нашей группы не получилось получить видимых бонусов с помощью них и мы думали о них как о занятных, но довольно бесполезных. Забавно, как драматически неверен этот вывод в другом контексте (Variational Autoencoder, Ladder Network и Generative Adversarial Network яркое тому доказательство).

