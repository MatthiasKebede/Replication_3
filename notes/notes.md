General Notes:
- Had issues cloning the Flakify on Windows due to filename `MAX_PATH` limitation, had to set `LongPathsEnabled` in the Registry Editor
- Python version 3.11.4 didn't work, tried 3.9 instead
- Had to download `codebert-base` files and use them locally since the URL wasn't resolving
- Used `dropna()` since the datasets allegedly had some NaN or missing values
- I was able to run the training scripts with only the above modifications, but since they took an unreasonable amount of time I ended up switching to Google Colab to access a GPU instance. This required some modification to update PyTorch API usage.
- The cross-validations crashed after overnight runs at the second-to-last line of the script, so I pulled the data from the console output and calculated the values by hand.
- The per-project runs take a huge amount of time to complete even the first of ~23 projects, so I have saved the logs I have and cut my losses.



Discrepancies:
- The updated replication package (GitHub) notes that a bugfix solves a data leakage but slightly lowers the accuracy of Flakify
