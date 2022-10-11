# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил(а):
- Загребин Данила Павлович
- РИ212702
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | # | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Познакомиться с программными средствами для организции передачи данных между инструментами google, Python и Unity

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity. 
Ход работы:
- В облачном сервисе google console подключить API для работы с google sheets и google drive.
 ![image](https://user-images.githubusercontent.com/114522298/195173475-9d4c43cd-1f62-4221-a049-6eaf1eac85d3.png)

 ![image](https://user-images.githubusercontent.com/114522298/195173501-4153a067-ea01-4008-8257-8022f3f29416.png)


- Реализовать запись данных из скрипта на python в google-таблицу. Данные описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с учётом стоимости игрового объекта в каждый период.
 ![image](https://user-images.githubusercontent.com/114522298/195173549-fd97f118-3c71-4ce1-b037-22bfe767560b.png)
![image](https://user-images.githubusercontent.com/114522298/195173592-406cd72d-06ef-4b7b-8ca9-58fcf92fc7db.png)


 
- Создать новый проект на Unity, который будет получать данные из google-таблицы, в которую были записаны данные в предыдущем пункте.  
![image](https://user-images.githubusercontent.com/114522298/195173636-95e153f7-60cd-4f7a-a5c5-4dbad4b78579.png)


- Написать функционал на Unity, в котором будет воспризводиться аудио-файл в зависимости от значения данных из таблицы.

'' Начало кода
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;

public AudioClip normalSpeak;

public AudioClip badSpeak;
private AudioSource selectAudio;
private Dictionary<string,float> dataSet = new Dictionary<string,float>();
private bool statusStart = false;
private int i = 1;


    void Start()
    {
       StartCoroutine(GoogleSheets());

    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1yCNUMDQsfWEJdTMeGwGsjSK8GJ6ZVnP269S-sDcEirY/values/Лист1?key=AIzaSyAHJY8hqDyNUacM0_fRdUAExpiNZ55ACLE");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp); 
        foreach (var itemRawJson in rawJson["values"])
        {
        var parseJson = JSON.Parse(itemRawJson.ToString());
        var selectRow = parseJson[0].AsStringList;
        dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    } 

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}
Конец кода



## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1. 
![image](https://user-images.githubusercontent.com/114522298/195174429-63e5768e-6ae2-48a0-a3c6-73f9652ec571.png)
![image](https://user-images.githubusercontent.com/114522298/195174459-35bd70f5-149e-498e-8258-06496ffbc93a.png)
#Import the required modules, numpy for calculation, and Matplotlib for drawing

Взял код из первой лабораторной и преобразовал его, чтобы связать с google таблицей.

import numpy as np
import gspread
gc = gspread.service_account(filename='lr-2-2-60c96be9dd05.json')
sh = gc.open("LR-2-2")
# define data, and change list to array
x = [3,21,22,34,54,34,55,67,89,99]
x = np.array(x)
y = [2,22,24,65,79,82,55,130,150,199]
y = np.array(y)


# define data, and change list to array
x = [3,21,22,34,54,34,55,67,89,99]
x = np.array(x)
y = [2,22,24,65,79,82,55,130,150,199]
y = np.array(y)

#The basic linear regression model is wx+ b, and since this is a two-dimensional space, the model is ax+ b

def model(a, b, x):
    return a*x + b

#Tahe most commonly used loss function of linear regression model is the loss function of mean variance difference
def loss_function(a, b, x, y):
    num = len(x)
    prediction=model(a,b,x)
    return (0.5/num) * (np.square(prediction-y)).sum()

#The optimization function mainly USES partial derivatives to update two parameters a and b
def optimize(a,b,x,y):
    num = len(x)
    prediction = model(a,b,x)
    #Update the values of A and B by finding the partial derivatives of the loss function on a and b
    da = (1.0/num) * ((prediction -y)*x).sum()
    db = (1.0/num) * ((prediction -y).sum())
    a = a - Lr*da
    b = b - Lr*db
    return a, b

#iterated function, return a and b
def iterate(a,b,x,y,times):
    for i in range(times):
        a,b = optimize(a,b,x,y)
    return a,b

#Initialize parameters and display
a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.000001

a,b = iterate(a,b,x,y,100)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)

mon = list(range(1,20))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        a, b = iterate(a, b, x, y, 100)
        prediction = model(a, b, x)
        loss = loss_function(a, b, x, y)
        tempInf = str(loss)
        tempInf = tempInf.replace('.', ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(tempInf))
        print(tempInf)


## Выводы

В лабораторной работе я познакомился с такими сервисами, как Google Cloud, Google sheets. Также я научился связывать программу написанную на языке Python с таблицей google Sheets, еще я научился реализовывать совместную работу и передачу данных в связке Python - Google-Sheets – Unity.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
