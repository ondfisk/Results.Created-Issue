# Results.Created returns a relative URI in the location header instead of an absolute (as in classic Web API)

## Using the standard template

```bash
dotnet new webapi --output Issue -minimal
```

with the following setup:

```csharp
app.MapPost("/weatherforecast", (WeatherForecast forecast) =>
{
    var id = 42;
    return TypedResults.Created($"/weatherforecast/{id}", forecast);
})
.WithName("PostWeatherForecast");
```

and calling the API with:

```http
POST http://localhost:5268/weatherforecast
{
  "date": "2022-11-15",
  "temperatureC": 10,
  "summary": "Cool"
}
```

yields the following headers:

```txt
content-type: application/json; charset=utf-8
date: Tue,15 Nov 2022 08:10:56 GMT
location: /weatherforecast/42
server: Kestrel
transfer-encoding: chunked
```

### Mapping with group

```csharp
var weather = app.MapGroup("/api/weatherforecast").WithOpenApi();

weather.MapPost("", (WeatherForecast forecast) =>
{
    var id = 42;
    return TypedResults.Created($"{id}", forecast);
})
.WithName("PostApiWeatherForecast")
.WithOpenApi();
```

and calling the API with:

```http
POST http://localhost:5268/api/weatherforecast
{
  "date": "2022-11-15",
  "temperatureC": 10,
  "summary": "Cool"
}
```

yields the following headers:

```txt
content-type: application/json; charset=utf-8
date: Tue,15 Nov 2022 08:11:26 GMT
location: 42
server: Kestrel
transfer-encoding: chunked
```

## Expected Behavior

In both cases above I would expect an absolute URI in the location header:

```txt
http://localhost:5268/weatherforecast/42
```

and

```txt
http://localhost:5268/api/weatherforecast/42
```
