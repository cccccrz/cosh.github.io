---
title: qt解析封装json
tags:
  - qt
categories:
  - qt
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-04-13 06:35:58/qt解析封装json.html
mathJax: false
date: 2023-04-13 14:35:58
thumbnail:
---
记录Qt的JSON解析封装用法
<!-- more -->

```cpp
QString PackWithUuid(const QJsonObject& obj)
{
    if (obj.isEmpty()) return QString();
    obj["uuid"] = "uuid";
    QJsonDocument doc(obj);
    return doc.toJson(QJsonDocument::Compact);
}

QString PackJson(const QString& text)
{
    QJsonObject obj;
    QJsonObject htmlObj;
    htmlObj["text"] = text;
    
    // array
    QJsonArray array;
    for (int i=0; i<5; ++i) {
        array.append(i);
    }
    htmlObj["array"] = array;
    obj["html"] = htmlObj;
	return PackWithUuid(obj);
}

bool ParseJson(const QByteArray& json, QString& text)
{
    QJsonParseError jsonError;
	QJsonDocument doc = QJsonDocument::fromJson(json, &jsonError);
    if (jsonError.error != QJsonParseError::NoError) {
        return false;
    }
    if (doc.isObject()) {
        QJsonObject obj = doc.object();
        if (obj.contains("html")) {
            if (obj["html"].isObject()) {
                QJsonObject htmlObj = obj["html"].toObject();
                if (htmlObj.contains("text")) {
                    if (htmlObj["text"].isString())
	                    text = htmlObj["text"].toString();
                }
                if (htmlObj.contains("array")) {
                    if (htmlObj["array"].isArray()) {
						QJsonArray array = htmlObj["array"].toArray();
                    }
                }
            }
        }
    }
    return true;
}

```

