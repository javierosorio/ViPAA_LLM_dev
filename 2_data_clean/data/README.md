# Content and description of the files

**The file "distributions_of_actions.xlsx"** shows:
    1. the list of unique type of actions with codes
    2. the list of unique type of actions clean
    3. the distribution of the cases (total number of cases for each type of action)
    4. the distribution of the cases (percentage in regard to the total number of cases)
    5. chart illustrating the distribution. 
The numbers in this file are extracted by the script **https://github.com/javierosorio/ViPAA_LLM_dev/tree/main/2_data_clean/scripts/actions_unique_n_distribution.ipynb**

The file "**_locations_unique.xlsx**” contains:
    1. a list of unique places (department & municipality) where the events take place and their frequency in the dataset and
    2. a list of unique departmens and their frequency. 
**Notes**
    -> "/" means no information about the location present in the original dataset.
    -> Some events have only the department and not info about the municipality
    -> There may be more than one location where events took place, they are separated by a comma, but are counted separately when giving stats by place. Usually these cases describe events that involve a large number of people.
    -> In the column of unique departments of Colombia there are 32 departmens of Colombia, Apure in Venezuela and Bogotá, D.C. as special administrative territory (34 in total)
The numbers in this file are extracted by the script 
**https://github.com/javierosorio/ViPAA_LLM_dev/tree/main/2_data_clean/scripts/extract_places_unique_n_frequency.ipynb**

**dataset_for_training.xlsx** is the version of the dataset that is processed by the script **"3_model/train.ipynb"**
