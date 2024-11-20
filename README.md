# Проектная работа «Резервное копирование» (API ВК)

## Описание проекта

### Суть проекта

Отправка фотографий из ВК на Яндекс диск по API. Также их можно отправить в Google Drive.


### Вводные параметры

ID пользователя ВК и токен из [Полигона](https://yandex.ru/dev/disk/poligon/) для подключения к Яндекс Диску.


### Ожидаемый результат

Создание фотографий пользователя ВК в специальной папке (по умолчанию VK_Images) и вывод json файла с основной информацией о них (название фотографии и [его формат](https://dev.vk.com/ru/reference/objects/photo-sizes)). По умолчанию программа выводит 5 фото.


### На какой версии Python выполнен проект?
Проект выполнен на Python 3.12.1 в Visual Studio Code (64-bit, Windows 10). Код для запуска API находится в файле API.py, а библиотеки для установки - в файле requirements.txt.


### Что следует сделать перед запуском программы?
Перед запуском кода удостоверьтесь, что:
- завели проект в Visual Studio Code или другом IDE
- в папке с проектом размещен json файл client_secrets.json из текущего репозитория (в случае работы с API Google Drive)

## Запуск программы
Для запуска кода необходимо ввести: 
- ```VK_ID``` - ID пользователя ВК
- ```YD_token``` - токен из [Полигона](https://yandex.ru/dev/disk/poligon/)

```python
# VK_ID - ID пользователя в ВК
# YD_token - токен для входа в API Яндекс Диска
if __name__ == "__main__":
    VK_ID = 111111111
    YD_token = "y0_AgAAAAAAaAA1AAAAAaAAAAA1AAAAAAAAAAAAA-AA1AaaAa1AaAaaa_aAaA"
    APIClient = VK_APIClient(VK_ID, YD_token)
    json_result = APIClient.TOTAL_upload_VK_photo()
    print(' ', 'Полученный json файл: ', json_result, sep = "\n")
```

## Алгоритм работы программы
После запуска кода будет осуществлено несколько последовательных шагов.

### **Шаг 1. Запрос к пользователю на получение токена ВК** 

В этом случае мы просим его войти по ссылке веб-приложения PyVkontakte (ID приложения - 51849067). После перехода токен копируется из ссылки новой страницы и вводится в поле ввода input (т.е. справа от надписи "Введите access_token:").


### **Шаг 2. Получение и обработка данных по фотографиям из ВК** 

Далее в случае корректного ввода токена программа достанет фотографии из ВК методом [photos.get](https://dev.vk.com/ru/method/photos.get) и обработает их в указанном количестве (по умолчению обрабатывается 5 фотографий).


### **Шаг 3. Отправка фотографий в Яндекс Диск** 

Отправляем фотографии методом POST в папку (по умолчению фотографии отправляются в папку VK_Images). Результат - создание (при необходимости) и заполнение папки в Яндекс Диске.


### **Шаг 4. Задаем вопрос "Вы хотите загрузить фото из ВК на Google Drive?"** 

В случае положительного ответа переходим к шагу 5, а в случае отрицательного ответа - завершаем программу и выводим json файл с основной информацией о фотографиях (название фото и [его формат](https://dev.vk.com/ru/reference/objects/photo-sizes)).


### **Шаг 5. Запуск API Google Drive (необходим файл client_secrets.json в папке проекта)** 

Подключаемся к API Google Drive (возможно предусмотренное переподключение по причине HttpAccessTokenRefreshError```*```). В случае успешного подключения к API программа проверит наличие папки в хранилище (в случае необходимости она ее создаст) и перенаправит фотографии из ВК. После успешного завршения шага выводится json файл с информацией о фотографиях.

```*``` - *В этом случае появится сообщение "Ошибка доступа к API Google Drive (HttpAccessTokenRefreshError). Повторное подключение к API..." и начнется повторное подключениие программы к API Google Drive*