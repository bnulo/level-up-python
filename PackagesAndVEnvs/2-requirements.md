## Requirements

```
pip freeze
```
lists all the installed packages

```
pip freeze > requirements.txt
```
Writes the list of all the required packages in text file like:
> certifi==2021.10.8
charset-normalizer==2.0.12
hazm==0.7.0
idna==3.3
libwapiti==0.2.1
nltk==3.3
numpy==1.21.6
protobuf==3.20.1
requests==2.27.1
six==1.16.0
stanfordnlp==0.2.0
torch==1.4.0
tqdm==4.64.0
typing-extensions==4.2.0
urllib3==1.26.9

Now for installing the packages listed in a requirements.txt file:

```
pip install -r NAME_OF_REQUIREMENTS_FILE
```
like:
```
pip install -r requirements.txt
```
