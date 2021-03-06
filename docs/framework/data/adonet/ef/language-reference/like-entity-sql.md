---
title: "LIKE (Entity SQL) | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-ado"
ms.tgt_pltfrm: ""
ms.topic: "article"
dev_langs: 
  - "VB"
  - "CSharp"
  - "C++"
ms.assetid: 8300e6d2-875b-481e-9ef4-e1e7c12d46fa
caps.latest.revision: 4
author: "JennieHubbard"
ms.author: "jhubbard"
manager: "jhubbard"
caps.handback.revision: 4
---
# LIKE (Entity SQL)
Определяет, соответствует ли аргумент типа `String` заданному шаблону.  
  
## Синтаксис  
  
```  
  
match [NOT] LIKE pattern [ESCAPE escape]  
```  
  
## Аргументы  
 `match`  
 Выражение [!INCLUDE[esql](../../../../../../includes/esql-md.md)], значением которого является `String`.  
  
 `pattern`  
 Шаблон для сопоставления с заданным значением `String`.  
  
 `escape`  
 Escape\-символ.  
  
 NOT  
 Указывает, что результат оператора LIKE должен быть инвертирован.  
  
## Возвращаемое значение  
 Равно `true`, если `string` соответствует шаблону, в противном случае равно `false`.  
  
## Заметки  
 В основном выражения [!INCLUDE[esql](../../../../../../includes/esql-md.md)], в которых используется оператор LIKE, вычисляются тем же способом, что и выражения, в которых в качестве условия фильтра используется оператор равенства. Однако выражения [!INCLUDE[esql](../../../../../../includes/esql-md.md)] с оператором LIKE могут включать как литералы, так и символы\-шаблоны.  
  
 В следующей таблице описан синтаксис шаблона `string`.  
  
|Символ\-шаблон|Описание|Пример|  
|--------------------|--------------|------------|  
|%|Любое значение `string` длиной от нуля и более символов.|По условию `title like '%computer%'`  будут найдены все названия, содержащие слово  `"computer"`  \(в любом месте названия\).|  
|\_ \(символ подчеркивания\)|Любой отдельный символ.|По условию `firstname like '_ean'`  будут найдены имена из четырех букв, которые оканчиваются на  `"ean`, например «Dean» или «Sean».|  
|\[ \]|Любой отдельный символ в диапазоне \(\[a\-f\]\) или наборе \(\[abcdef\]\).|По условию `lastname like '[C-P]arsen'`  будут найдены фамилии, которые оканчиваются на «arsen», а начинаются с любого символа между «C» и «P», например «Carsen» или «Larsen».|  
|\[^\]|Любой символ, содержащийся в диапазоне \(\[^a\-f\]\) или наборе \(\[^abcdef\]\).|По условию `lastname like 'de[^l]%'`  будут найдены все фамилии, которые начинаются с символов «de» и не включают символа «l» в качестве следующего символа.|  
  
> [!NOTE]
>  Оператор LIKE и предложение ESCAPE языка [!INCLUDE[esql](../../../../../../includes/esql-md.md)] не могут применяться к значениям `System.DateTime` и `System.Guid`.  
  
 Оператор LIKE поддерживает сравнение по шаблонам в ASCII и Юникоде. Если все параметры имеют символьный тип ASCII, то применяется шаблон ASCII. Если один или несколько аргументов представлен в Юникоде, то выполняется преобразование всех аргументов в Юникод, а затем применяется шаблон в Юникоде. Завершающие пробелы учитываются только при работе оператора LIKE с данными в Юникоде. Для других типов данных завершающие пробелы не учитываются. Строка шаблона языка [!INCLUDE[esql](../../../../../../includes/esql-md.md)] имеет тот же синтаксис, что и [!INCLUDE[tsql](../../../../../../includes/tsql-md.md)].  
  
 Шаблон может включать в себя обычные символы и символы\-шаблоны. Во время сравнения с шаблоном обычные символы должны в точности совпадать с символами, присутствующими в `string`. Символы\-шаблоны могут совпадать с произвольными элементами символьной строки. При использовании символов\-шаблонов оператор LIKE более гибок, нежели операторы сравнения строки \= и \!\=.  
  
> [!NOTE]
>  Для работы с конкретным поставщиком могут использоваться расширения, специфические для поставщика. Однако такие конструкции другими поставщиками могут обрабатываться иначе. Например, SqlServer поддерживает шаблоны \[первый\-последний\] и \[^первый\-последний\], в которых первый сопоставляет ровно один символ между первым и последним, а последний \- ровно один символ, который не находится между первым и последним.  
  
### ESC  
 Предложение ESCAPE позволяет искать символьные строки, в состав которых входит один или более специальных символов\-шаблонов, описанных в таблице в прошлом разделе. Предположим, нужно найти все документы, содержащие в заголовке литерал «100%». Поскольку символ процента \(%\) является символом\-шаблоном, для успешного выполнения поиска необходимо экранировать его при помощи предложения ESCAPE языка [!INCLUDE[esql](../../../../../../includes/esql-md.md)]. Ниже приведен пример этого фильтра.  
  
```  
"title like '%100!%%' escape '!'"  
```  
  
 В этом выражении поиска символ\-шаблон процента \(%\), немедленно следующий за символом восклицательного знака \(\!\), рассматривается как литерал, а не как символ\-шаблон. В качестве escape\-символа можно использовать любой символ, кроме символов\-шаблонов [!INCLUDE[esql](../../../../../../includes/esql-md.md)] и символов квадратных скобок \(`[ ]`\). В прошлом примере символ восклицательного знака \(\!\) используется как escape\-символ.  
  
## Пример  
 В следующих двух запросах [!INCLUDE[esql](../../../../../../includes/esql-md.md)] операторы LIKE и ESCAPE определяют, соответствует ли символьная строка заданному шаблону. Первый запрос выполняет поиск `Name`, начинающегося с символов `Down_`. В этом запросе используется параметр ESCAPE, так как символ подчеркивания \(`_`\) является символом\-шаблоном. Без указания параметра ESCAPE запрос будет выполнять поиск любых значений `Name`, начинающихся со слова `Down`, за которым следует любой единичный символ, отличный от символа подчеркивания. Запросы основаны на модели AdventureWorks Sales. Для компиляции и запуска этого запроса выполните следующие шаги.  
  
1.  Выполните процедуру из статьи [Как выполнить запрос, возвращающий типы\-примитивы](../../../../../../docs/framework/data/adonet/ef/how-to-execute-a-query-that-returns-primitivetype-results.md).  
  
2.  Передайте следующий запрос в качестве аргумента методу `ExecutePrimitiveTypeQuery`:  
  
 [!code-csharp[DP EntityServices Concepts 2#LIKE](../../../../../../samples/snippets/csharp/VS_Snippets_Data/dp entityservices concepts 2/cs/entitysql.cs#like)]  
  
## См. также  
 [Справочник по Entity SQL](../../../../../../docs/framework/data/adonet/ef/language-reference/entity-sql-reference.md)