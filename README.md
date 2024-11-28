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
### STEP 03- install the requirements
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

Now,
```bash
http://127.0.0.1:80 #Open this url in the browser
```