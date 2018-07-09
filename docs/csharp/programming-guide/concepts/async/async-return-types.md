---
title: Асинхронные типы возвращаемых значений (C#)
ms.date: 05/29/2017
ms.assetid: ddb2539c-c898-48c1-ad92-245e4a996df8
ms.openlocfilehash: 07aefcf3149b2210e3dc97713647fa3a0133a535
ms.sourcegitcommit: 3d5d33f384eeba41b2dff79d096f47ccc8d8f03d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33334188"
---
# <a name="async-return-types-c"></a>Асинхронные типы возвращаемых значений (C#)
Асинхронные методы могут иметь следующие типы возвращаемых значений:

- <xref:System.Threading.Tasks.Task%601> для асинхронного метода, возвращающего значение. 
 
-  <xref:System.Threading.Tasks.Task> для асинхронного метода, который выполняет операцию, но не возвращает значение.

- `void` для обработчика событий. 

- Начиная с версии 7.0 в языке C# поддерживаются любые типы с доступным методом `GetAwaiter`. Объект, возвращаемый методом `GetAwaiter`, должен реализовывать интерфейс <xref:System.Runtime.CompilerServices.ICriticalNotifyCompletion?displayProperty=nameWithType>.
  
Дополнительные сведения об асинхронных методах см. в разделе [Асинхронное программирование с использованием ключевых слов Async и Await (C#)](../../../../csharp/programming-guide/concepts/async/index.md).  
  
Каждый тип возвращаемого значения рассматривается в одном из следующих разделов, а полный пример, в котором используются все три типа, вы найдете в конце этого раздела.  
  
##  <a name="BKMK_TaskTReturnType"></a> Тип возвращаемого значения Task(T)  
Тип возвращаемого значения <xref:System.Threading.Tasks.Task%601> используется для асинхронного метода, содержащего инструкцию [return](../../../../csharp/language-reference/keywords/return.md) (C#), в которой операнд имеет тип `TResult`.  
  
В следующем примере асинхронный метод `GetLeisureHours` содержит инструкцию `return`, которая возвращает целое число. Поэтому в объявлении метода должен указываться тип возвращаемого значения `Task<int>`.  Асинхронный метод <xref:System.Threading.Tasks.Task.FromResult%2A> представляет собой заполнитель для операции, которая возвращает строку.
  
[!code-csharp[return-value](../../../../../samples/snippets/csharp/programming-guide/async/async-returns1.cs)]

При вызове `GetLeisureHours` из выражения await в методе `ShowTodaysInfo` это выражение await извлекает целочисленное значение (значение `leisureHours`), хранящееся в задаче, которая возвращается методом `GetLeisureHours`. Дополнительные сведения о выражениях await см. в разделе [await](../../../../csharp/language-reference/keywords/await.md).  
  
Чтобы лучше понять, как это происходит, отделите вызов метода `GetLeisureHours` от применения `await`, как показано в следующем коде. Вызов метода `TaskOfT_MethodAsync`, который не ожидается немедленно, возвращает `Task<int>`, как и следовало ожидать из объявления метода. В данном примере эта задача назначается переменной `integerTask`. Поскольку `integerTask` является <xref:System.Threading.Tasks.Task%601>, она содержит свойство <xref:System.Threading.Tasks.Task%601.Result> типа `TResult`. В этом примере TResult представляет собой целочисленный тип. Если выражение `await` применяется к `integerTask`, выражение await вычисляется как содержимое свойства <xref:System.Threading.Tasks.Task%601.Result%2A> объекта `integerTask`. Это значение присваивается переменной `result2`.  
  
> [!IMPORTANT]
>  Свойство <xref:System.Threading.Tasks.Task%601.Result%2A> является блокирующим свойством. При попытке доступа к нему до завершения его задачи поток, который в текущий момент активен, блокируется до того момента, пока задача не будет завершена, а ее значение не станет доступным. В большинстве случаев следует получать доступ к этому значению с помощью `await` вместо прямого обращения к свойству. <br/> В предыдущем примере извлекалось значение свойства <xref:System.Threading.Tasks.Task%601.Result%2A> для блокировки основного потока. Это позволяет закончить выполнение метода `ShowTodaysInfo` до того, как завершится работа приложения.  

[!code-csharp[return-value](../../../../../samples/snippets/csharp/programming-guide/async/async-returns1a.cs#1)]
  
##  <a name="BKMK_TaskReturnType"></a> Тип возвращаемого значения Task  
Асинхронные методы, не содержащие инструкцию `return` или содержащие инструкцию `return`, которая не возвращает операнд, обычно имеют тип возвращаемого значения <xref:System.Threading.Tasks.Task>. При синхронном выполнении такие методы возвращают `void`. Если для асинхронного метода вы используете тип возвращаемого значения <xref:System.Threading.Tasks.Task>, вызывающий метод может использовать оператор `await` для приостановки выполнения вызывающего объекта до завершения вызванного асинхронного метода.  
  
В следующем примере асинхронный метод `WaitAndApologize` не содержит инструкцию `return`, в связи с чем он возвращает объект <xref:System.Threading.Tasks.Task>. Это позволяет реализовать ожидание `WaitAndApologize`. Обратите внимание, что тип <xref:System.Threading.Tasks.Task> не имеет возвращаемого значения и, соответственно, не содержит свойство `Result`.  

[!code-csharp[return-value](../../../../../samples/snippets/csharp/programming-guide/async/async-returns2.cs)]  
  
`WaitAndApologize` вызывается и ожидается с помощью инструкции await (вместо выражения await), похожей на инструкцию вызова для синхронного метода, возвращающего значение void. Применение оператора await в этом случае не возвращает значение.  
  
Как и в предыдущем примере <xref:System.Threading.Tasks.Task%601>, вы можете отделить вызов `WaitAndApologize` от применения инструкции await, как показывает следующий код. Однако следует помнить, что `Task` не содержит свойство `Result`, и при применении оператора await к `Task` никакое значение не создается.  
  
В следующем коде вызов метода `WaitAndApologize` отделяется от ожидания задачи, которую возвращает этот метод.  
 
[!code-csharp[return-value](../../../../../samples/snippets/csharp/programming-guide/async/async-returns2a.cs#1)]  
 
##  <a name="BKMK_VoidReturnType"></a> Тип возвращаемого значения Void  
Тип возвращаемого значения `void` используется в асинхронных обработчиках событий, для которых требуется тип возвращаемого значения `void`. Поскольку методы, не являющиеся обработчиками событий, не возвращают значения, вместо этого необходимо вернуть <xref:System.Threading.Tasks.Task>. Это вызвано тем, что для асинхронных методов, возвращающих значение `void`, ожидание невозможно. Любой вызывающий объект такого метода должен иметь возможность завершить свою работу, не дожидаясь завершения вызванного асинхронного метода, и он не должен зависеть ни от каких значений и исключений, создаваемых асинхронным методом.  
  
Вызывающий объект асинхронного метода, возвращающего void, не может перехватывать исключения, создаваемые методом, и такие необработанные исключения могут привести к сбою приложения. Если исключение возникает в асинхронном методе, который возвращает <xref:System.Threading.Tasks.Task> или <xref:System.Threading.Tasks.Task%601>, исключение хранится в возвращенной задаче и повторно вызывается при ожидании задачи. Поэтому убедитесь, что любой асинхронный метод, который может вызвать исключение, имеет тип возвращаемого значения <xref:System.Threading.Tasks.Task> или <xref:System.Threading.Tasks.Task%601> и что вызовы метода являются ожидаемыми.  
  
Дополнительные сведения о перехвате исключений в асинхронных методах см. в разделе [try-catch](../../../../csharp/language-reference/keywords/try-catch.md).  
  
Следующий фрагмент кода определяет асинхронный обработчик событий.  
 
[!code-csharp[return-value](../../../../../samples/snippets/csharp/programming-guide/async/async-returns3.cs)]  
 
## <a name="generalized-async-return-types-and-valuetaskt"></a>Обобщенные асинхронные типы возвращаемых значений и ValueTask<T>

Начиная с C# 7.0 асинхронные методы могут возвращать любой тип, имеющий доступный метод `GetAwaiter`.
 
Поскольку <xref:System.Threading.Tasks.Task> и <xref:System.Threading.Tasks.Task%601> являются ссылочными типами, выделение памяти во влияющих на производительность сегментах (особенно при выделении памяти в ограниченных циклах) может серьезно снизить производительность. Поддержка обобщенных типов возвращаемых значений позволяет возвращать небольшой тип значения вместо ссылочного типа, благодаря чему удается предотвратить избыточное выделение памяти. 

На платформе .NET представлена структура <xref:System.Threading.Tasks.ValueTask%601?displayProperty=nameWithType>, которая является упрощенной реализацией обобщенного значения, возвращающего задачу. Чтобы использовать тип <xref:System.Threading.Tasks.ValueTask%601?displayProperty=nameWithType>, необходимо добавить в проект пакет NuGet `System.Threading.Tasks.Extensions`. В следующем примере структура <xref:System.Threading.Tasks.ValueTask%601> используется для извлечения значений двух игральных костей. 
  
[!code-csharp[return-value](../../../../../samples/snippets/csharp/programming-guide/async/async-valuetask.cs)]

## <a name="see-also"></a>См. также  
<xref:System.Threading.Tasks.Task.FromResult%2A>   
[Пошаговое руководство. Получение доступа к Интернету с помощью модификатора Async и оператора Await (C#)](../../../../csharp/programming-guide/concepts/async/walkthrough-accessing-the-web-by-using-async-and-await.md)   
[Поток управления в асинхронных программах (C#)](../../../../csharp/programming-guide/concepts/async/control-flow-in-async-programs.md)   
[async](../../../../csharp/language-reference/keywords/async.md)   
[await](../../../../csharp/language-reference/keywords/await.md)
