
import streamlit as st
import requests
import pandas as pd
import plotly.express as px

# OpenWeatherMap API Key (Replace 'YOUR_API_KEY' with your actual API Key)
API_KEY = "9477ae73bd875b03eb838b2afbb023a5"
BASE_URL = "https://api.openweathermap.org/data/2.5/weather?q={city name}&appid={API key}"

# Function to fetch weather data
def get_weather(city):
    url = f"https://api.openweathermap.org/data/2.5/weather?q={city}&appid={API_KEY}&units=metric"
    response = requests.get(url)
    if response.status_code == 200:
        return response.json()
    else:
        return None

# Streamlit UI
st.set_page_config(page_title="Weather Prediction System", layout="wide")
st.title("🌦 Weather Prediction System")

# User input for city
city = st.text_input("Enter City Name", "New York")

if st.button("Get Weather"):
    weather_data = get_weather(city)
    if weather_data:
        col1, col2 = st.columns(2)
        with col1:
            st.subheader(f"Weather in {city}")
            st.write(f"Temperature: {weather_data['main']['temp']}°C")
            st.write(f"Humidity: {weather_data['main']['humidity']}%")
            st.write(f"Weather: {weather_data['weather'][0]['description'].capitalize()}")
        with col2:
            icon_code = weather_data['weather'][0]['icon']
            st.image(f"http://openweathermap.org/img/wn/{icon_code}@2x.png", width=100)
    else:
        st.error("City not found. Please enter a valid city.")

# Analysis Dashboard (Mock Data for Visualization)
st.subheader("📊 Weather Analysis Dashboard")
data = {
    "City": ["New York", "London", "Tokyo", "Delhi", "Sydney"],
    "Temperature (°C)": [22, 18, 27, 30, 25],
    "Humidity (%)": [60, 75, 65, 55, 70],
    "Wind Speed (km/h)": [12, 8, 15, 10, 14]
}
df = pd.DataFrame(data)

# Temperature Chart
fig_temp = px.bar(df, x="City", y="Temperature (°C)", title="Temperature Comparison", color="Temperature (°C)", text_auto=True)
st.plotly_chart(fig_temp, use_container_width=True)

# Humidity Chart
fig_humidity = px.line(df, x="City", y="Humidity (%)", title="Humidity Levels", markers=True)
st.plotly_chart(fig_humidity, use_container_width=True)

# Wind Speed Chart
fig_wind = px.scatter(df, x="City", y="Wind Speed (km/h)", title="Wind Speed Across Cities", size="Wind Speed (km/h)", color="City")
st.plotly_chart(fig_wind, use_container_width=True)

st.write("📌 Data Source: OpenWeatherMap API & Mock Data for Visualization")
