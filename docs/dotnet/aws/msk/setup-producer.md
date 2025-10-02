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
var config = new ProducerConfig
{
    BootstrapServers = "<HOST>:<PORT>",
    Acks = Acks.None,
    SecurityProtocol = SecurityProtocol.SaslSsl,
    SaslMechanism = SaslMechanism.OAuthBearer
};
```

### 4. Setup ProducerBuilder with SchemaRegistry
---

```cs
var schemaRegistryConfig = new SchemaRegistryConfig
{
    Url = "http://<scheamRegistryHost>:<Port>";
};

var schemaRegistry = new CachedSchemaRegistryClient(schemaRegistryConfig);

service.AddSingleton<IProducer<Null, signup_db_topic_value>>(_ => 
    new ProducerBuilder<Null, signup_db_topic_value>(config)
        .SetKeySerializer(new AvroSerializer<Null>(schemaRegistry))
        .SetValueSerializer(new AvroSerializer<signup_db_topic_value>(schemaRegistry))
        .SetOAuthBearerTokenRefreshHandler(OauthCallback).Build());
```