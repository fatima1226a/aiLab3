# Анализ предпочтений пользователей и рекомендательная система для онлайн-кинотеатра

Проект по обработке данных и извлечению структурированных рекомендаций фильмов на основе истории просмотров пользователей с использованием **Gemini API** (`google-genai`) и валидации данных с помощью **Pydantic v2**.

## 📌 Описание проекта
Система анализирует историю просмотров онлайн-кинотеатра (жанры, пользовательские оценки от 1 до 5, время просмотра и процент досмотра). На основе этих данных рассчитывается профиль предпочтений пользователя, который затем передается языковой модели для формирования персональных рекомендаций с точно заданным форматом ответа (JSON).

## 🛠 Технологии и стек
* **Язык программирования:** Python 3.10+
* **LLM API:** Google Gemini API (`gemini-3.5-flash-lite`) via `google-genai`
* **Валидация данных:** `pydantic v2` (использование `response_schema`)
* **Анализ и подготовка данных:** `pandas`
* **Среда выполнения:** Google Colab

## 📂 Структура проекта
* `Lab03_Cinema_Recommendation.ipynb` — Google Colab ноутбук с полным циклом генерации данных, анализа и вызовов API.
* `recommendations_results.csv` — итоговый CSV-файл с полученными рекомендациями, процентом соответствия (`match_score`) и текстовым обоснованием (`reasoning`).
* `README.md` — документация к проекту.

## ⚙️ Схема данных Pydantic

Для гарантии получения строго структурированного ответа от модели используется следующая схема:

```python
from pydantic import BaseModel, Field
from typing import List

class MovieRecommendation(BaseModel):
    movie_title: str = Field(description="Название рекомендуемого фильма")
    genre: str = Field(description="Жанр фильма")
    match_score: int = Field(ge=1, le=100, description="Процент соответствия вкусам от 1 до 100")
    reasoning: str = Field(description="Обоснование рекомендации на основе истории просмотров")

class RecommendationResponse(BaseModel):
    user_id: str
    recommendations: List[MovieRecommendation] = Field(min_length=3, max_length=5)
