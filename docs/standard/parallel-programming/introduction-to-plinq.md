---
title: Введение в PLINQ
description: Сведения о том, как параллельно выполнять запросы с помощью PLINQ в .NET. PLINQ означает "параллельный LINQ" (Language-Integrated Query).
ms.date: 03/30/2017
ms.technology: dotnet-standard
dev_langs:
- csharp
- vb
helpviewer_keywords:
- PLINQ queries, introduction to
ms.assetid: eaa720d8-8999-4eb7-8df5-3c19ca61cad0
ms.openlocfilehash: 9dbc4fde3f72d01aee91978ed5cb0baf0895de26
ms.sourcegitcommit: 7137e12f54c4e83a94ae43ec320f8cf59c1772ea
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84662463"
---
# <a name="introduction-to-plinq"></a>Введение в PLINQ

Parallel LINQ (PLINQ) является параллельной реализацией шаблона [ LINQ (Language-Integrated Query)](../../csharp/programming-guide/concepts/linq/index.md). PLINQ реализует полный набор стандартных операторов запроса LINQ как методов расширения для пространства имен <xref:System.Linq> и имеет дополнительные операторы для параллельных операций. PLINQ объединяет простоту и удобство чтения синтаксиса LINQ с мощностью параллельного программирования.

> [!TIP]
> Если вы не знакомы с LINQ, он поддерживает унифицированную модель для запросов к любому перечислимому источнику данных типобезопасным образом. LINQ to Objects — это запросы LINQ, выполняемые с коллекциями в памяти (например, <xref:System.Collections.Generic.List%601>) или массивами. В этой статье предполагается, что у вас уже есть общие представления о LINQ. Дополнительные сведения см. в статье [Синтаксис LINQ](../../csharp/programming-guide/concepts/linq/index.md).

## <a name="what-is-a-parallel-query"></a>Что такое параллельный запрос?

Запрос PLINQ во многом напоминает непараллельный запрос LINQ to Objects. Запросы PLINQ, как и последовательные запросы LINQ, работают с любым источником данных <xref:System.Collections.IEnumerable> или <xref:System.Collections.Generic.IEnumerable%601> в памяти и поддерживают отложенное выполнение, т. е. выполнение только по завершении перечисления запроса. Основное различие состоит в том, что PLINQ пытается задействовать сразу все процессоры в системе. Для этого он разбивает источник данных на сегменты, а затем запрашивается каждый сегмент в отдельном рабочем потоке сразу, используя сразу несколько процессоров. Во многих случаях параллельное выполнение значительно сокращает время выполнения запроса.

Благодаря параллельному выполнению PLINQ позволяет существенно повысить производительность некоторых видов запросов по сравнению с устаревшим кодом. Часто для этого достаточно добавить к источнику данных оператор запроса <xref:System.Linq.ParallelEnumerable.AsParallel%2A>. Тем не менее параллелизм может представлять свои собственные сложности, и не все операции запросов в PLINQ выполняются быстрее. Некоторые запросы при применении параллелизма только замедляются. В связи с этим необходимо понимать, как влияют на параллельные запросы такие аспекты, как упорядочение. Дополнительные сведения см. в разделе [Общее представление об ускорении выполнения в PLINQ](understanding-speedup-in-plinq.md).

> [!NOTE]
> В этой документации для определения делегатов в PLINQ используются лямбда-выражения. Если вы не знакомы с лямбда-выражениями в C# или Visual Basic, см. раздел [Лямбда-выражения в PLINQ и TPL](lambda-expressions-in-plinq-and-tpl.md).

Далее в этой статье приводится обзор основных классов PLINQ и обсуждаются способы создания запросов PLINQ. Каждый раздел содержит ссылки на более подробные сведения и примеры кода.

## <a name="the-parallelenumerable-class"></a>Класс ParallelEnumerable

Класс <xref:System.Linq.ParallelEnumerable?displayProperty=nameWithType> предоставляет почти все функциональные возможности PLINQ. Этот класс и остальные типы пространства имен <xref:System.Linq?displayProperty=nameWithType> компилируются в сборку System.Core.dll. Проекты C# и Visual Basic по умолчанию в Visual Studio ссылаются на сборку и импортируют пространство имен.

<xref:System.Linq.ParallelEnumerable> содержит реализации всех стандартных операторов запроса, поддерживаемых LINQ to Objects, но не все из них пытается выполнять параллельно. Если вы не использовали LINQ, см. общие сведения о LINQ для [C#](../../csharp/programming-guide/concepts/linq/index.md) и [Visual Basic](../../visual-basic/programming-guide/concepts/linq/introduction-to-linq.md).

Помимо стандартных операторов запроса, класс <xref:System.Linq.ParallelEnumerable> содержит набор методов для реализации функций, характерных для параллельного выполнения. Методы, характерные для PLINQ, перечислены в следующей таблице.

|Класс ParallelEnumerable|Описание|
|---------------------------------|-----------------|
|<xref:System.Linq.ParallelEnumerable.AsParallel%2A>|Точка входа для PLINQ. Указывает, что по возможности остальная часть запроса должна быть параллелизована.|
|<xref:System.Linq.ParallelEnumerable.AsSequential%2A>|Указывает, что остальная часть запроса должна выполняться последовательно, как непараллельный запрос LINQ.|
|<xref:System.Linq.ParallelEnumerable.AsOrdered%2A>|Указывает, что PLINQ должен сохранить порядок исходной последовательности до конца запроса либо до тех пор, пока порядок не изменится, что может произойти, например, при использовании предложения orderby (Order By в Visual Basic).|
|<xref:System.Linq.ParallelEnumerable.AsUnordered%2A>|Указывает, что PLINQ для остальной части запроса не обязан сохранять порядок исходной последовательности.|
|<xref:System.Linq.ParallelEnumerable.WithCancellation%2A>|Указывает, что PLINQ должен периодически отслеживать состояние предоставленного токена отмены и отменить выполнение, если он будет запрошен.|
|<xref:System.Linq.ParallelEnumerable.WithDegreeOfParallelism%2A>|Указывает максимальное количество процессоров, которое PLINQ должен использовать для параллелизации запроса.|
|<xref:System.Linq.ParallelEnumerable.WithMergeOptions%2A>|Предоставляет подсказку о том, каким образом PLINQ должен объединять параллельные результаты в одну последовательность в потоке-потребителе, если это возможно.|
|<xref:System.Linq.ParallelEnumerable.WithExecutionMode%2A>|Указывает, должен ли PLINQ параллелизовать запрос, даже если по умолчанию он должен выполняться последовательно.|
|<xref:System.Linq.ParallelEnumerable.ForAll%2A>|Многопоточный метод перечисления в отличие от итерации результатов запроса может обрабатываться параллельно без предварительного объединения с потоком-потребителем.|
|Перегрузка <xref:System.Linq.ParallelEnumerable.Aggregate%2A>|Перегрузка, которая является уникальной для PLINQ и обеспечивает промежуточное агрегирование локальных разделов потока, а также функцию окончательного агрегирования, позволяющую объединять результаты всех разделов.|

## <a name="the-opt-in-model"></a>Модель с явным согласием

Когда вы создаете запрос, подтвердите согласие на использование PLINQ вызовом метода расширения <xref:System.Linq.ParallelEnumerable.AsParallel%2A?displayProperty=nameWithType> для источника данных, как показано в следующем примере.

[!code-csharp[PLINQ#1](../../../samples/snippets/csharp/VS_Snippets_Misc/plinq/cs/plinq2_cs.cs#1)]
[!code-vb[PLINQ#1](../../../samples/snippets/visualbasic/VS_Snippets_Misc/plinq/vb/plinq2_vb.vb#1)]

Метод расширения <xref:System.Linq.ParallelEnumerable.AsParallel%2A> привязывает последующие операторы запросов (в нашем примере это `where` и `select`) к реализациям <xref:System.Linq.ParallelEnumerable?displayProperty=nameWithType>.

## <a name="execution-modes"></a>Режимы выполнения

По умолчанию PLINQ является консервативным. Во время выполнения инфраструктура PLINQ анализирует общую структуру запроса. Если параллелизация может ускорить выполнение запроса, PLINQ разбивает исходную последовательность на задачи, которые выполняются одновременно. Если параллелизовать запрос небезопасно, PLINQ просто выполняет его последовательно. Если PLINQ может выбирать между потенциально затратным параллельным алгоритмом или нетребовательным последовательным алгоритмом, по умолчанию он выбирает алгоритм последовательной обработки. Метод <xref:System.Linq.ParallelEnumerable.WithExecutionMode%2A> и перечисление <xref:System.Linq.ParallelExecutionMode?displayProperty=nameWithType> позволяют указать, что PLINQ следует выбрать параллельный алгоритм. Это пригодится в том случае, если тестирование и измерение показали, что в параллельном режиме определенный запрос будет выполнять быстрее. Дополнительные сведения см. в разделе [Практическое руководство. Задание режима выполнения в PLINQ](how-to-specify-the-execution-mode-in-plinq.md).

## <a name="degree-of-parallelism"></a>Степень параллелизма

По умолчанию PLINQ использует все процессоры на главном компьютере. Вы можете ограничить число процессоров, используемых PLINQ, с помощью метода <xref:System.Linq.ParallelEnumerable.WithDegreeOfParallelism%2A>. Это пригодится в том случае, если вам нужно, чтобы другие процессы, выполняемые на том же компьютере, получали определенное количество процессорного времени. Следующий фрагмент кода позволяет запросу использовать не более двух процессоров.

[!code-csharp[PLINQ#5](../../../samples/snippets/csharp/VS_Snippets_Misc/plinq/cs/plinqsamples.cs#5)]
[!code-vb[PLINQ#5](../../../samples/snippets/visualbasic/VS_Snippets_Misc/plinq/vb/plinq2_vb.vb#5)]

В случаях, когда запрос выполняет значительный объем работы, не связанной с вычислениями, такой как ввод-вывод файлов, степень параллелизма может быть больше количества ядер на соответствующем компьютере.

## <a name="ordered-versus-unordered-parallel-queries"></a>Упорядоченные и неупорядоченные параллельные запросы

В некоторых случаях оператор запроса должен выдавать результаты с сохранением порядка исходной последовательности. Для этого PLINQ предоставляет оператор <xref:System.Linq.ParallelEnumerable.AsOrdered%2A>. <xref:System.Linq.ParallelEnumerable.AsOrdered%2A> отличается от <xref:System.Linq.ParallelEnumerable.AsSequential%2A>. Последовательность <xref:System.Linq.ParallelEnumerable.AsOrdered%2A> по-прежнему обрабатывается параллельно, но ее результаты помещаются в буфер и сортируются. Поскольку сохранение порядка обычно требует дополнительной работы, последовательность <xref:System.Linq.ParallelEnumerable.AsOrdered%2A> может обрабатываться медленнее, чем стандартная последовательность <xref:System.Linq.ParallelEnumerable.AsUnordered%2A>. Будет ли та или иная упорядоченная параллельная операция выполняться быстрее, чем ее последовательная версия, зависит от многих факторов.

В следующем примере кода показано, как разрешить сохранение порядка.

[!code-csharp[PLINQ#3](../../../samples/snippets/csharp/VS_Snippets_Misc/plinq/cs/plinq2_cs.cs#3)]
[!code-vb[PLINQ#3](../../../samples/snippets/visualbasic/VS_Snippets_Misc/plinq/vb/plinq2_vb.vb#3)]

Дополнительные сведения см. в разделе [Сохранение порядка в PLINQ](order-preservation-in-plinq.md).

## <a name="parallel-vs-sequential-queries"></a>Сравнение параллельных и последовательных запросов

Некоторые операции требуют, чтобы исходные данные доставлялись последовательно. При необходимости операторы запроса <xref:System.Linq.ParallelEnumerable> автоматически переходят в последовательный режим. Для пользовательских операторов запроса и делегатов, которые требуют последовательного выполнения, PLINQ предоставляет метод <xref:System.Linq.ParallelEnumerable.AsSequential%2A>. При использовании метода <xref:System.Linq.ParallelEnumerable.AsSequential%2A> все операторы, содержащиеся в запросе, будут выполняться последовательно вплоть до следующего вызова <xref:System.Linq.ParallelEnumerable.AsParallel%2A>. Дополнительные сведения см. в разделе [Практическое руководство. Объединение параллельных и последовательных запросов LINQ](how-to-combine-parallel-and-sequential-linq-queries.md).

## <a name="options-for-merging-query-results"></a>Параметры для слияния результатов запроса

При параллельном выполнении запроса PLINQ его результаты из каждого рабочего потока должны быть снова объединены с основным потоком для использования циклом `foreach` (`For Each` в Visual Basic) либо вставки в список или массив. В некоторых случаях может быть полезно указать конкретный вид операции слияния, например для того, чтобы получать результаты быстрее. Для этого PLINQ поддерживает метод <xref:System.Linq.ParallelEnumerable.WithMergeOptions%2A> и перечисление <xref:System.Linq.ParallelMergeOptions>. Дополнительные сведения см. в разделе [Параметры слияние в PLINQ](merge-options-in-plinq.md).

## <a name="the-forall-operator"></a>Оператор ForAll

В последовательных запросах LINQ выполнение откладывается до того момента, пока запрос не будет перечислен в цикле `foreach` (`For Each` в Visual Basic) или будет вызван метод <xref:System.Linq.ParallelEnumerable.ToList%2A>, <xref:System.Linq.ParallelEnumerable.ToArray%2A> или <xref:System.Linq.ParallelEnumerable.ToDictionary%2A>. Кроме того, для выполнения запроса и итерации результатов в PLINQ можно использовать `foreach`. При этом сам оператор `foreach` параллельно не выполняется, а значит результаты всех параллельных задач необходимо снова объединить с тем потоком, в котором выполняется цикл. Оператор `foreach` можно использовать в PLINQ, если вам нужно сохранить окончательный порядок результатов запроса, а также при любой последовательной обработке результатов (например, при вызове `Console.WriteLine` для каждого элемента). Чтобы ускорить выполнение запроса в ситуации, когда сохранение порядка не требуется и обработка результатов допускает параллелизацию, используйте для выполнения запроса PLINQ метод <xref:System.Linq.ParallelEnumerable.ForAll%2A>. <xref:System.Linq.ParallelEnumerable.ForAll%2A> не выполняет этот заключительный шаг слияния. В следующем примере кода показано применение метода <xref:System.Linq.ParallelEnumerable.ForAll%2A>. <xref:System.Collections.Concurrent.ConcurrentBag%601?displayProperty=nameWithType> используется здесь потому, что он оптимизирован для одновременного добавления данных из нескольких потоков и не пытается удалять элементы.

[!code-csharp[PLINQ#4](../../../samples/snippets/csharp/VS_Snippets_Misc/plinq/cs/plinq2_cs.cs#4)]
[!code-vb[PLINQ#4](../../../samples/snippets/visualbasic/VS_Snippets_Misc/plinq/vb/plinq2_vb.vb#4)]

Ниже демонстрируется разница между `foreach` и <xref:System.Linq.ParallelEnumerable.ForAll%2A> в выполнении запросов.

![Сравнение ForAll и ForEach](media/vs-isvnt-allvseach.png "VS_ISVNT_ALLvsEACH")

## <a name="cancellation"></a>Отмена

PLINQ интегрирован с типами отмены в .NET Framework 4. (Дополнительные сведения см. в разделе [Отмена в управляемых потоках](../threading/cancellation-in-managed-threads.md).) Это значит, что в отличие от последовательных запросов LINQ to Objects запросы PLINQ можно отменять. Чтобы создать запрос PLINQ с возможностью отмены, примените в запросе оператор <xref:System.Linq.ParallelEnumerable.WithCancellation%2A> и предоставьте ему экземпляр <xref:System.Threading.CancellationToken> в качестве аргумента. Когда свойство <xref:System.Threading.CancellationToken.IsCancellationRequested%2A> для маркера примет значение TRUE, PLINQ заметит это и остановит обработку всех потоков, а затем создаст исключение <xref:System.OperationCanceledException>.

Существует вероятность, что запрос PLINQ продолжит обработку некоторых элементов после того, как будет задан маркер отмены.

Для повышения скорости реагирования можно также отвечать на запросы отмены в пользовательских делегатах, выполняемых долгое время. Дополнительные сведения см. в разделе [Практическое руководство. Отмена запроса PLINQ](how-to-cancel-a-plinq-query.md).

## <a name="exceptions"></a>Исключения

При выполнении запроса PLINQ могут быть одновременно выданы сразу несколько исключений из разных потоков. Кроме того, код для обработки исключения может находиться не в том потоке, где находится код, который вызвал исключение. С помощью типа <xref:System.AggregateException> PLINQ инкапсулирует все исключения, созданные запросом, и маршалирует эти исключения в вызывающий поток. В вызывающем потоке должен присутствовать только один блок try-catch. Но в нем вы можете последовательно просмотреть все инкапсулированные в <xref:System.AggregateException>исключения и обработать те из них, которые допускают безопасное восстановление. В редких случаях могут создаваться исключения, не упакованные в <xref:System.AggregateException>. Также этот механизм не применяется для <xref:System.Threading.ThreadAbortException>.

Если исключения могут всплывать обратно в присоединяемый поток, запрос может продолжить обработку некоторых элементов после создания исключения.

Дополнительные сведения см. в разделе [Практическое руководство. Обработка исключений в запросе PLINQ](how-to-handle-exceptions-in-a-plinq-query.md).

## <a name="custom-partitioners"></a>Пользовательские разделители

В некоторых случаях производительность запросов можно улучшить, написав пользовательский модуль разделения, который использует преимущества некоторых характеристик исходных данных. В запросе сам пользовательский модуль разделения является запрашиваемым перечислимым объектом.

[!code-csharp[PLINQ#2](../../../samples/snippets/csharp/VS_Snippets_Misc/plinq/cs/plinq2_cs.cs#2)]
[!code-vb[PLINQ#2](../../../samples/snippets/visualbasic/VS_Snippets_Misc/plinq/vb/plinq3.vb#2)]

PLINQ поддерживает фиксированное количество разделов (хотя для балансировки нагрузки данные могут динамически переназначаться этим разделам во время выполнения). <xref:System.Threading.Tasks.Parallel.For%2A> и <xref:System.Threading.Tasks.Parallel.ForEach%2A> поддерживают только динамическое секционирование, а значит количество секций может изменяться во время выполнения. Дополнительные сведения см. в разделе [Пользовательские разделители для PLINQ и TPL](custom-partitioners-for-plinq-and-tpl.md).

## <a name="measuring-plinq-performance"></a>Измерение производительности PLINQ

Во многих случаях запрос может выполняться параллельно, но на настройку параллельного запроса уходит больше времени, чем будет выиграно в результате. Если запрос не выполняет большой объем вычислений или источник данных небольшой, запрос PLINQ может быть медленнее, чем последовательный запрос LINQ to Objects. Анализатор параллельной производительности в Visual Studio Team Server позволяет сравнивать производительность различных запросов и таким образом выявлять проблемы обработки и определять, выполняется ли запрос параллельно или последовательно. Дополнительные сведения см. в статье [Визуализатор параллелизма](/visualstudio/profiling/concurrency-visualizer) и [Практическое руководство. Измерение производительности запросов PLINQ](how-to-measure-plinq-query-performance.md).

## <a name="see-also"></a>См. также

- [Parallel LINQ (PLINQ)](introduction-to-plinq.md)
- [Общее представление об ускорении выполнения в PLINQ](understanding-speedup-in-plinq.md)
