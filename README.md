# InsightText

Clone the repository

```bash
https://github.com/SunilKumar-ugra/BBC_News_Classification_LSTM.git
```
### STEP 01- Create a conda environment after opening the repository

```bash
conda create -n rayserve python=3.10 -y
```

```bash
conda activate rayserve
```


### STEP 02- install the requirements
```bash
pip install -r requirements.txt
```
### STEP 03- To run the application 
##### Method1
```bash
# Finally run the following command
serve main:endpoint_app
```
##### Method2
Below step is OPTIONAL if serve_config.ymal NOT FOUND
```bash
serve build main:endpoint_app -o serve_config.yaml
```
Run the rayserve 
```
serve deploy serve_config.yaml
```
### Test the application
Now, open new terminal and run the below code to test
```bash 
python
import requests
send_text = {'text':'''
    Generative AI, or generative artificial intelligence, is a type of AI that can create new content, such as text, images, videos, music, and audio. It uses machine learning techniques to learn from existing data and generate new data with similar characteristics. 
Generative AI
How it works
Uses foundation models, which are large AI models that can perform tasks like summarization, Q&A, and classification
What it can do
Create new content, learn human language, programming languages, art, chemistry, biology, or any complex subject matter
Applications
Arts and entertainment, technology and communications, customer experience, software development, digital labor, science, engineering, and research
Generative AI can be powerful, but its outputs aren't always accurate or appropriate. For example, generative AI models can reflect and amplify any cultural bias present in the underlying data. Some examples of generative AI models include:GPT-3.5A foundation model trained on large volumes of text that can be adapted for answering questions, text summarization, or sentiment analysis DALL-EA multimodal (text-to-image) foundation model that can be adapted to create images, expand images beyond their original size, or create variations of existing paintings 
'''
}
response = requests.post("http://127.0.0.1:8000/", json=send_text)
response.text


```