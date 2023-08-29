# Кабина спячки

Разбор статьи [DreamBooth: Fine Tuning Text-to-Image Diffusion Models for Subject-Driven Generation](https://arxiv.org/abs/2208.12242) и тестирование подхода.

[Эксперименты в коллабе](https://colab.research.google.com/github/axchizhov/kabina_spyachky/blob/main/kill_booth.ipynb)

## Проблема

Умение рисовать — удовольствие дорогое. Для приобретения этого навыка людям требуется 5-10 лет упорной работы, и большинство из нас рисовать не умеет.

С 1970х годов люди пробовали научить рисовать компьютер, но заменить художника на машину до недавнего времени никак не удавалось. Однако, 

С 1970х годов люди пробовали заменить художника на машину, но до недавнего времени этого не получалось. Но десять лет назад появились первые успешные попытки — искусственная нейросеть генерировала, хотя и с огрехами, правдоподобные фотографии и рисунки. А пару лет назад на свет появился подход, который позволяет создавать по словесному описанию практически любые изображения. 

Компьютер рисует, но остается проблемой получить точные изображения

Уже не так далеко время, когда ..., но еще остаются нерешенные проблемы
но генерация все еще имеет некоторые ограничения

В этом направлении предстоит решить еще много задач, но, в целом, уже не так далеко время, когда каждый желающий сможет носить персонального художника у себя в кармане.



Одной из пока нерешенных проблем в генерации изображений является few-shot image generation — генерация изображений под конкретный домен

ПРИМЕР: по небольшому набору (5-10) изображений собаки научиться генерировать эту собаку в новых ракурсах, позах, етц

ИЛЛЮСТРАЦИЯ

- большинство генеративных сетей не могут perform few shot learning
    - нет эффективного метода для инъекции субъекта в модель при тренировка
        - оверфиттинг
        - language drift
        example: gan reproduce the same face given 100 examples

DreamBooth is a method to personalize text2image models like stable diffusion given just a few(3~5) images of a subject. The train_dreambooth.py script shows how to implement the training procedure and adapt it for stable diffusion.

## Решение

В чем ее основная идея?
В чем ее новаторство?

contibutions

Авторы предложили эффективную технику файнт-тюнинга обученной модели, которая preserves semantic class knowledge (prior knowledge that model already has, eg the model knows what the dog does, how its mount looks like)
    tuning requires only 3-5 examples

create (formulate) new problem -- subject driven generation -> maintain fidelity in new contexts

В чем суть подхода:
- переобучить токен
- новая функция потерь для сохранения приоров



Какие получились результаты?

(ИЛЛЮСТРАЦИЯ с часами)

ИЛЛЮСТРАЦИЯ с корги


[Title](https://www.youtube.com/watch?v=D641lhioXMc)



Матильда приглашает в killbooth из футурамы

## Эксперименты

- [ ] Разложить по чекбоксам

1. Оптимизации из коробки, чтобы быстрее итерироваться
fp16 - в два раза быстрее, визуально нет отличий
memory efficient attention (xformers)

![runtime](runtime.png)

2. увеличим количество эпох
100
300
600
900
1200
2400
делить на 4 для получения эпох

рекомендованные 300 эпох
недоообучение до
переобучени после

КАРТИНКИ

3. подключить class preservation loss

для предотвращения overfitting

слишком долго считается -- как-нибудь в другой раз, отключаем пока

поможет с бонусной частью


3.  подключить текст энкодер



4. prompt

a gun -- сложно






- попробовать другой токен
- попробовать более простой аксессуар (вместо глока)


- опробовать разные наборы датасеетов

## Установка

install imagemagic
for i in *.png; do magick convert "$i" -auto-orient -thumbnail 512x512^ -gravity center -extent 512x512 "./crops/$i"; done

for i in *.png; do magick convert "$i" -auto-orient -thumbnail 768x768^ -gravity center -extent 768x768 "$i"; done
