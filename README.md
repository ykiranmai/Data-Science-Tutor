import streamlit as st
import google.generativeai as genai
from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_core.prompts import ChatPromptTemplate

# Configure Google AI API
genai.configure(api_key=st.secrets["general"]["google_ai"])

# Initialize LangChain Model
def get_ai_response(question):
    model = ChatGoogleGenerativeAI(model="gemini-1.5-flash")
    prompt = ChatPromptTemplate.from_messages([
        ("system", "You are an expert Data Science tutor. Answer only Data Science-related questions. "
                   "If the question is unrelated to Data Science, politely refuse to answer."),
        ("user", "{question}")
    ])
    chain = prompt | model
    response = chain.invoke({"question": question})
    
    # Ensure polite refusal
    if "data science" not in response.content.lower():
        return "I'm here to help with Data Science-related topics. Please ask something related to Data Science. "
    
    return response.content

# Streamlit UI
st.title(" AI Conversational Data Science Tutor")

st.write("Ask anything about Data Science, and AI will assist you!")

# User Input
user_question = st.text_area("Enter your Data Science question:", height=150)

# Process Query
if st.button("Get Answer"):
    if user_question.strip():
        result = get_ai_response(user_question)
        st.subheader("AI Response:")
        st.write(result)
    else:
        st.warning("Please enter a question before submitting.")
