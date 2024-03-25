# Italian Parliamentary Speeches and other Acts - IPSA corpus

## Data

The corpus is available in TXT format by following this [link to the OneDrive shared folder](https://fbk-my.sharepoint.com/:f:/g/personal/aprosio_fbk_eu/Ev5-RQdKAPZGq3C82r9P7Z8B7DANV-EY59TceMMW0AMFiQ).

## Steps
Scripts should be executed in order:
- first raw dataframes are preprocessed
- dictionaries for the symspell phase are created based on the dataframes
- dictionaries are merged in windows around the legislature in order to make correction more robust
- the evaluation is conducted with the gold standard: uncorrected texts, symspell-corrected texts using the base it-100k dictionary and the symspell-corrected texts using the window dictionaries are compared
- the correction of all documents is conducted
## Phases in-depth explaination
### Preprocessing
The dataframe of each document page is cleaned in the following manner:
- truncated words are merged
- double column pages are merged
- intestation is removed with a rule-based method (each document type is accounted for)
Finally the content of each page is written on to a txt file.
In this phase, the pages also present in the gold_standard folder are automatically extracted for the evaluation phase. 
### Dictionary creation
Dictionaries are created in the following manner:
- the raw dataframe are cleaned from: whitespaces, truncated words, words under a user-specified confidence (tesseract)
- words are counted and inserted into a Dictionary class for each document, for each legislature
- the resulting dictionary is cut by a criterion chosen by the user, either top k words by frequency are kept, or the words with a frequency higher than a threshold
- dictionary is saved in a txt file in the format: <word> <frequency>
Once a dictionary is created for each legislature, new dictionaries can be created by merging them: this can make the symspell correction more robust.
The method used for this project is the following: for each legislature a new dictionary is created by merging those in a time window around it, e.g. assuming the user-specified parameter "span" is 5, the dictionary for regno_04 will be created by merging those of regno_02, regno_03, regno_04, regno_05, regno_06.
### Evaluation
The evaluation is conducted by comparing the gold standard to:
- the uncorrected documents (the output of the preprocessing phase)
- the documents corrected by symspell using the base symspell it-100k dictionary
- the documents corrected by symspell using the span dictionaries
The evaluation metrics used are [Word Error Rate](https://en.wikipedia.org/wiki/Word_error_rate) and [Character Error Rate](https://torchmetrics.readthedocs.io/en/stable/text/char_error_rate.html).
### Correction
The correction of the documents is conducted by iterating over all preprocessed documents and correcting them with symspell and their span dictionaries.
## Suggested project directory structure:
```bash
project/
│
├── README.md
│
├── data/                                  # data
│   ├── raw_data/ 
│   ├── preprocessed_data/ 
│   └── corrected_data/   
│
├── scripts/                               # Rest of the code in the repo
│   ├── cleaning_utils/
│   ├── requirements.txt
│   ├── 0-preprocessing.py   
│   ├── 1-create_dict.py   
│   └── ...         
│   
└── evaluation/                            #folder containing evaluation sets
    ├── gold_standard/
    │   ├── cam-leg-date-doc-page.txt
    │   ├── cam-leg-date-doc-page.txt   
    │   └── ...
    ├── uncorrected_set/
    │   ├── cam-leg-date-doc-page.txt   
    │   ├── cam-leg-date-doc-page.txt  
    │   └── ...
    ├── base_symspell_set/
    │   ├── cam-leg-date-doc-page.txt
    │   ├── cam-leg-date-doc-page.txt
    │   └── ...
    └── window_dict_symspell_set/
        ├── cam-leg-date-doc-page.txt  
        ├── cam-leg-date-doc-page.txt 
        └── ...
```
## ToDo
- Add more gold standard documents (50 total at least)
- Implement re-introduction of punctuation in corrected texts
- Implement t-test in evaluation phase
- Test different confidence cutoffs and cut criteria of dictionaries
