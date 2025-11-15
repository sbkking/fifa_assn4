METRICS = {
    "temp": Gauge("weather_temperature_c", "Air temperature", ["city"]),
    "feels_like": Gauge("weather_feels_like_c", "Feels like", ["city"]),
    "pressure": Gauge("weather_pressure_hpa", "Pressure", ["city"]),
    "humidity": Gauge("weather_humidity_pct", "Humidity", ["city"]),
    "wind_speed": Gauge("weather_wind_speed_ms", "Wind speed", ["city"]),
    "wind_deg": Gauge("weather_wind_deg", "Wind direction deg", ["city"]),
    "clouds": Gauge("weather_clouds_pct", "Cloudiness", ["city"]),
    "visibility": Gauge("weather_visibility_m", "Visibility", ["city"]),
    "rain": Gauge("weather_rain_1h_mm", "Rain last 1h mm", ["city"]),
    "snow": Gauge("weather_snow_1h_mm", "Snow last 1h mm", ["city"]),
}

def get_weather(city):
    url = f"https://api.openweathermap.org/data/2.5/weather?q={city}&appid={API_KEY}&units=metric"
    data = requests.get(url).json()
    return {
        "temp": data["main"]["temp"],
        "feels_like": data["main"]["feels_like"],
        "pressure": data["main"]["pressure"],
        "humidity": data["main"]["humidity"],
        "wind_speed": data["wind"]["speed"],
        "wind_deg": data["wind"]["deg"],
        "clouds": data["clouds"]["all"],
        "visibility": data.get("visibility", 0),
        "rain": data.get("rain", {}).get("1h", 0),
        "snow": data.get("snow", {}).get("1h", 0),
    }

if name == "main":
    start_http_server(8000)
    while True:
        for city in ["Astana", "Almaty", "London"]:
            try:
                w = get_weather(city)
                for k, v in w.items():
                    METRICS[k].labels(city=city).set(v)
                print(f"Updated weather for {city}: {w['temp']}°C")
            except Exception as e:
                print("Error:", e)
        time.sleep(20)




✅ All dashboards verified and visualized in Grafana (real-time updates every 20 seconds).
