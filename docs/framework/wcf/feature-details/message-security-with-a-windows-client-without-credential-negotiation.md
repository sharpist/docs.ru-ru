---
title: "Безопасность сообщений с использованием клиента Windows без согласования учетных данных | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-clr"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: fc07a26c-cbee-41c5-8fb0-329085fef749
caps.latest.revision: 18
author: "BrucePerlerMS"
ms.author: "bruceper"
manager: "mbaldwin"
caps.handback.revision: 18
---
# Безопасность сообщений с использованием клиента Windows без согласования учетных данных
В следующем сценарии показаны клиент и служба [!INCLUDE[indigo1](../../../../includes/indigo1-md.md)], защищенные протоколом Kerberos.  
  
 Клиент и служба находятся в одном и том же домене или в доверенных доменах.  
  
> [!NOTE]
>  Различие между этим сценарием и сценарием [Безопасность сообщений с клиентом Windows](../../../../docs/framework/wcf/feature-details/message-security-with-a-windows-client.md) заключается в том, что данный сценарий не выполняет согласование учетных данных службы перед отправкой сообщения приложения.  И так как для этого требуется протокол Kerberos, для этого сценария необходима среда домена Windows.  
  
 ![Безопасность сообщений без согласования учетных данных](../../../../docs/framework/wcf/feature-details/media/0c9f9baa-2439-4ef9-92f4-43c242d85d0d.gif "0c9f9baa\-2439\-4ef9\-92f4\-43c242d85d0d")  
  
|Характеристика|Описание|  
|--------------------|--------------|  
|Режим безопасности|Сообщение|  
|Взаимодействие|Да, WS\-Security с клиентами, совместимыми с профилем маркера Kerberos|  
|Проверка подлинности \(сервера\)|Взаимная проверка подлинности сервера и клиента|  
|Проверка подлинности \(клиента\)|Взаимная проверка подлинности сервера и клиента|  
|Целостность|Да|  
|Конфиденциальность|Да|  
|Transport|HTTP|  
|Привязка|<xref:System.ServiceModel.WSHttpBinding>|  
  
## Служба  
 Предполагается, что представленные ниже код и конфигурация выполняются независимо.  Выполните одно из следующих действий.  
  
-   Создайте автономную службу, используя код без конфигурации.  
  
-   Создайте службу, используя предоставленную конфигурацию, но не определяйте конечные точки.  
  
### Код  
 В следующем коде создается конечная точка службы, которая использует безопасность сообщений.  Этот код отключает согласование учетных данных службы и установку маркера контекста безопасности \(SCT\).  
  
> [!NOTE]
>  Для использования типа учетных данных Windows без согласования учетная запись пользователя службы должна иметь доступ к имени участника\-службы \(SPN\), зарегистрированному с доменом Active Directory.  Для этого существуют два способа:  
  
1.  Используйте учетную запись `NetworkService` или `LocalSystem` для запуска службы.  Поскольку у этих учетных записей есть право доступа к SPN компьютера, устанавливаемому при присоединении компьютера к домену Active Directory, [!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] автоматически создает соответствующий элемент SPN в конечной точке службы в метаданных службы \(языке описания веб\-служб или языке WSDL\).  
  
2.  Запустите службу из любой учетной записи домена Active Directory.  В этом случае потребуется установить SPN для учетной записи домена.  Это можно сделать, например, с помощью средства Setspn.exe.  Создав SPN для учетной записи службы, задайте [!INCLUDE[indigo2](../../../../includes/indigo2-md.md)] публиковать это SPN в клиентах службы через метаданные \(WSDL\).  Это можно сделать, настроив удостоверение конечной точки для предоставляемой конечной точки либо в файле конфигурации приложения, либо в коде.  В следующем примере описывается программный способ публикации удостоверения.  
  
 [!INCLUDE[crabout](../../../../includes/crabout-md.md)]б именах участника\-службы, протоколе Kerberos и Active Directory см. в разделе [Техническое дополнение Kerberos для Windows](http://go.microsoft.com/fwlink/?LinkId=88330).  [!INCLUDE[crabout](../../../../includes/crabout-md.md)] удостоверениях конечных точек см. в разделе [Режимы проверки подлинности SecurityBindingElement](../../../../docs/framework/wcf/feature-details/securitybindingelement-authentication-modes.md).  
  
 [!code-csharp[C_SecurityScenarios#12](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_securityscenarios/cs/source.cs#12)]
 [!code-vb[C_SecurityScenarios#12](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_securityscenarios/vb/source.vb#12)]  
  
### Конфигурация  
 Вместо кода можно использовать следующую конфигурацию.  
  
```  
<?xml version="1.0" encoding="utf-8"?>  
<configuration>  
  <system.serviceModel>  
    <behaviors />  
    <services>  
      <service behaviorConfiguration="" name="ServiceModel.Calculator">  
        <endpoint address="http://localhost/Calculator"   
                  binding="wsHttpBinding"  
                  bindingConfiguration="KerberosBinding"  
                  name="WSHttpBinding_ICalculator"  
                  contract="ServiceModel.ICalculator"   
                  listenUri="net.tcp://localhost/metadata" >  
         <identity>  
            <servicePrincipalName value="service_spn_name" />  
         </identity>  
        </endpoint>  
      </service>  
    </services>  
    <bindings>  
      <wsHttpBinding>  
        <binding name="KerberosBinding">  
          <security>  
            <message negotiateServiceCredential="false"   
                     establishSecurityContext="false" />  
          </security>  
        </binding>  
      </wsHttpBinding>  
    </bindings>  
    <client />  
  </system.serviceModel>  
</configuration>  
```  
  
## Клиент  
 Предполагается, что представленные ниже код и конфигурация выполняются независимо.  Выполните одно из следующих действий.  
  
-   Создайте автономный клиент, используя код \(и код клиента\).  
  
-   Создайте клиент, который не определяет никаких адресов конечных точек.  Вместо этого используйте конструктор клиента, который принимает в качестве аргумента имя конфигурации.  Например:  
  
     [!code-csharp[C_SecurityScenarios#0](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_securityscenarios/cs/source.cs#0)]
     [!code-vb[C_SecurityScenarios#0](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_securityscenarios/vb/source.vb#0)]  
  
### Код  
 Следующий код служит для настройки клиента.  Для режима безопасности задано значение Message, типу учетных данных клиента присвоено значение Windows.  Обратите внимание: свойствам <xref:System.ServiceModel.MessageSecurityOverHttp.NegotiateServiceCredential%2A> и <xref:System.ServiceModel.NonDualMessageSecurityOverHttp.EstablishSecurityContext%2A> задается значение `false`.  
  
> [!NOTE]
>  Для использования типа учетных данных Windows без согласования требуется настроить SPN учетной записи службы до начала обмена данными со службой.  Клиент использует имя SPN, чтобы получить маркер Kerberos для проверки подлинности и обеспечения безопасности обмена данными со службой.  В следующем образце показано, как настроить SPN службы для клиента.  Если для создания клиента используется средство [Служебное средство ServiceModel Metadata Utility Tool \(Svcutil.exe\)](../../../../docs/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe.md), то имена участников\-служб автоматически согласуются с клиентом на основе метаданных службы \(WSDL\), если в метаданных службы содержится эта информация.  [!INCLUDE[crabout](../../../../includes/crabout-md.md)] том, как настроить службу для включения ее имени участника\-службы в метаданные, см. в подразделе «Служба» далее в этом разделе.  
>   
>  Дополнительные сведения об именах участника\-службы, протоколе Kerberos и Active Directory см. в разделе [Техническое дополнение Kerberos для Windows](http://go.microsoft.com/fwlink/?LinkId=88330).  [!INCLUDE[crabout](../../../../includes/crabout-md.md)] удостоверениях конечных точек см. в разделе [Режимы проверки подлинности SecurityBindingElement](../../../../docs/framework/wcf/feature-details/securitybindingelement-authentication-modes.md).  
  
 [!code-csharp[C_SecurityScenarios#19](../../../../samples/snippets/csharp/VS_Snippets_CFX/c_securityscenarios/cs/source.cs#19)]
 [!code-vb[C_SecurityScenarios#19](../../../../samples/snippets/visualbasic/VS_Snippets_CFX/c_securityscenarios/vb/source.vb#19)]  
  
### Конфигурация  
 Следующий код служит для настройки клиента.  Обратите внимание: элемент [\<servicePrincipalName\>](../../../../docs/framework/configure-apps/file-schema/wcf/serviceprincipalname.md) должен соответствовать SPN службы, установленному для учетной записи службы в домене Active Directory.  
  
```  
<?xml version="1.0" encoding="utf-8"?>  
<configuration>  
  <system.serviceModel>  
    <bindings>  
      <wsHttpBinding>  
        <binding name="WSHttpBinding_ICalculator" >  
          <security mode="Message">  
            <message clientCredentialType="Windows"   
                     negotiateServiceCredential="false"  
                     establishSecurityContext="false" />  
          </security>  
        </binding>  
      </wsHttpBinding>  
    </bindings>  
    <client>  
      <endpoint address="http://localhost/Calculator"   
                binding="wsHttpBinding"  
                bindingConfiguration="WSHttpBinding_ICalculator"  
                contract="ICalculator"  
                name="WSHttpBinding_ICalculator">  
        <identity>  
          <servicePrincipalName value="service_spn_name" />  
        </identity>  
      </endpoint>  
    </client>  
  </system.serviceModel>  
</configuration>  
```  
  
## См. также  
 [Общие сведения о безопасности](../../../../docs/framework/wcf/feature-details/security-overview.md)   
 [Идентификация и проверка подлинности службы](../../../../docs/framework/wcf/feature-details/service-identity-and-authentication.md)   
 [Модель безопасности для Windows Server App Fabric](http://go.microsoft.com/fwlink/?LinkID=201279&clcid=0x409)