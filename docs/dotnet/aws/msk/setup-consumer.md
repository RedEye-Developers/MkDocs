### 1. Install Required Nuget Package
---

```
- AWS.MSK.Auth
- Streamiz.Kafka.Net.SchemaRegistry.SerDes.Avro
```

### 2. Setup OAuthCallBack Function
---

```cs
var mskAuthTokenGenerator = new AWSMSKAuthTokenGenerator();

private void OauthCallback(IClient client, string cfg)
{
    try
    {
        var (token, expiryMs) = mskAuthTokenGenerator.GenerateAuthToken(Amazon.RegionEndpoint.APSouth1); //Change The RegionEndpoint for Your MSK Region.
        client.OAuthBearerSetToken(token, expiryMs, "DummyPrincipal");
    }
    catch (Exception e)
    {
        client.OAuthBearerSetTokenFailure(e.ToString());
    }
}
```

### 3. ConsumerConfig Setup
---

```cs
var config = new ConsumerConfig()
{
    BootstrapServers = "<HOST>:<PORT>",
    GroupId = "Kafka-Consumer",
    AutoOffsetReset = AutoOffsetReset.Latest,
    SecurityProtocol = SecurityProtocol.SaslSsl,
    SaslMechanism = SaslMechanism.OAuthBearer
};
```

### 4. Setup ConsumerBuild
---
#### With ScheamRegistry

```cs
var schemaRegistryConfig = new SchemaRegistryConfig
{
    Url = "http://<scheamRegistryHost>:<Port>";
};
    
var schemaRegistry = new CachedSchemaRegistryClient(schemaRegistryConfig);

service.AddSingleton<IConsumer<Null, signup_db_topic_value>>(_ => 
    new ConsumerBuilder<Null, signup_db_topic_value>(consumerConfig)
        .SetKeyDeserializer(new AvroDeserializer<Null>(schemaRegistry).AsSyncOverAsync())
        .SetValueDeserializer(new AvroDeserializer<signup_db_topic_value>(schemaRegistry).AsSyncOverAsync())
        .SetOAuthBearerTokenRefreshHandler(OauthCallback).Build());
```                

#### Without ScheamRegistry

```cs
var consumerBuilder = new ConsumerBuilder<string, string>(consumerConfig).SetOAuthBearerTokenRefreshHandler(OauthCallback).Build();

consumerBuilder.Subscribe("Kafka-Consumer-Topic");

Console.WriteLine("Kafka Start Listening...");
    
while (true)
{
    var consumer= consumerBuilder.Consume();
    Console.WriteLine(consumer.Message.Value);
}
```