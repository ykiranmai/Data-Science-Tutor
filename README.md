# Data-Science-Tutor
import streamlit as st
import google.generativeai as genai
from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_core.prompts import ChatPromptTemplate


genai.configure(api_key=st.secrets["general"]["google_ai"])


def get_ai_response(question):
    model = ChatGoogleGenerativeAI(model="gemini-1.5-flash")
    prompt = ChatPromptTemplate.from_messages([
        ("system", "You are an expert Data Science tutor. Answer questions clearly."),
        ("user", "{question}")
    ])
    chain = prompt | model
    response = chain.invoke({"question": question})
    return response.content


st.title(" AI Conversational Data Science Tutor (LangChain + GoogleAI)")

st.write("Ask any Data Science question, and AI will assist you!")


user_question = st.text_area("Enter your Data Science question:", height=150)


if st.button("Get Answer"):
    if user_question.strip():
        result = get_ai_response(user_question)
        st.subheader("AI Response:")
        st.write(result)
    else:
        st.warning("Please enter a question before submitting.")
