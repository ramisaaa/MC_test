# Form Parser GCP Parser service
The goal of this service is to recreate the original text from the ocr result process.
Return also an augmented_text that include in the right position the table structured data.

## Service description

- run.sh # run the service with the dockerfile and config
- .env # config
- api.py # main file
- dependencies/* # module for your logic
- testing/api_test.py # test file launch with test.sh
- datamode.py # pydantic model for input/output
- Dockerfile # build the docker image



## Input

POST /api/microservice/parser/gcp-form-parser-json
-d '{
    # a valid json output from the form parser
    # an example can be found inside testing/input/gcp-form-parser-*.json
}'

## Output
{
    status_code: 200|400|500, # 200 if the request was successful, 400 if the error handle is checked, 500 if an unexpected erro occurred
    message: "Success"|"Error message",
    output: {
        text: "The original text from the ocr result",
        augmented_text: "The original text with the table structured data included in the right position"
    }
}

### Augmented Text definition
The augmented text is the original text with the table structured data included in the right position.
We define a table as tsv with the first row containing the column names and the following rows containing the data.
We include the table in the augmented text by adding a tag <tableau> at the beginning of the table and </tableau> at the end of the table.
```python
"""
Le Titre du document
Je suis un paragraphe normal, je n’ai pas de double colonne et l’ocr est suffisant
pour extraire un texte de qualité.

<tableau>
Colonne n°1\t Colonne n°2\t Colonne n°3\t Colonne n°4\n
cellule 1\t colonne 1 cellule 1\t colonne 2 cellule 1\t colonne 3 cellule 1\t colonne 4\n
cellule 2\t colonne 1 cellule 2\t colonne 2 cellule 2\t colonne 3 cellule 2\t colonne 4\n
cellule 3\t colonne 1 cellule 3\t colonne 2 cellule 3\t colonne 3 cellule 3\t colonne 4\n
</tableau>

Example tableau simple
Je suis un deuxième paragraphe normal, je n’ai pas de double colonne et l’ocr est suffisant
pour extraire un texte de qualité.
Je suis un troisième paragraphe normal, je n’ai pas de double colonne et l’ocr est suffisant
pour extraire un texte de qualité.
Je suis un quatrième paragraphe normal, je n’ai pas de double colonne et l’ocr est suffisant
pour extraire un texte de qualité.
Page n°1
[...]
"""
```

