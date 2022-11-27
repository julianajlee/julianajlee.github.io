---
title: "CS50-AI Week 4. Project 1: Shopping"
permalink: /_pages/cs50/week4-shopping
tags:
  - cs50
  - Python
toc: true
---

```python
import csv
import sys

from sklearn.model_selection import train_test_split
from sklearn.neighbors import KNeighborsClassifier

TEST_SIZE = 0.4


def main():

    # Check command-line arguments
    if len(sys.argv) != 2:
        sys.exit("Usage: python shopping.py data")

    # Load data from spreadsheet and split into train and test sets
    evidence, labels = load_data(sys.argv[1])
    X_train, X_test, y_train, y_test = train_test_split(
        evidence, labels, test_size=TEST_SIZE
    )

    # Train model and make predictions
    model = train_model(X_train, y_train)
    predictions = model.predict(X_test)
    sensitivity, specificity = evaluate(y_test, predictions)

    # Print results
    print(f"Correct: {(y_test == predictions).sum()}")
    print(f"Incorrect: {(y_test != predictions).sum()}")
    print(f"True Positive Rate: {100 * sensitivity:.2f}%")
    print(f"True Negative Rate: {100 * specificity:.2f}%")


def load_data(filename):
    """
    Load shopping data from a CSV file `filename` and convert into a list of
    evidence lists and a list of labels. Return a tuple (evidence, labels).
    evidence should be a list of lists, where each list contains the
    following values, in order:
        - Administrative, an integer
        - Administrative_Duration, a floating point number
        - Informational, an integer
        - Informational_Duration, a floating point number
        - ProductRelated, an integer
        - ProductRelated_Duration, a floating point number
        - BounceRates, a floating point number
        - ExitRates, a floating point number
        - PageValues, a floating point number
        - SpecialDay, a floating point number
        - Month, an index from 0 (January) to 11 (December)
        - OperatingSystems, an integer
        - Browser, an integer
        - Region, an integer
        - TrafficType, an integer
        - VisitorType, an integer 0 (not returning) or 1 (returning)
        - Weekend, an integer 0 (if false) or 1 (if true)
    labels should be the corresponding list of labels, where each label
    is 1 if Revenue is true, and 0 otherwise.
    """

    evidence = []
    labels = []

    # Dictionary of months to their corresponding numerical values
    months = {
        'Jan': 0,
        'Feb': 1,
        'Mar': 2,
        'Apr': 3,
        'May': 4,
        'June': 5,
        'Jul': 6,
        'Aug': 7,
        'Sep': 8,
        'Oct': 9,
        'Nov': 10,
        'Dec': 11
    }

    # Visitor types as integers
    visitors = {'Returning_Visitor': 1, 'New_Visitor': 0, 'Other': 0}

    bools = {'TRUE': 1, 'FALSE': 0}

    # CSV file is loaded as dictionary

    with open(filename, 'r') as f:

        csv_reader = csv.DictReader(f, delimiter=',')

        for row_user in csv_reader:

            evidence.append([
                int(row_user["Administrative"]),
                float(row_user["Administrative_Duration"]),
                int(row_user["Informational"]),
                float(row_user['Informational_Duration']),
                int(row_user['ProductRelated']),
                float(row_user['ProductRelated_Duration']),
                float(row_user['BounceRates']),
                float(row_user['ExitRates']),
                float(row_user['PageValues']),
                float(row_user['SpecialDay']),
                months[row_user['Month']],
                int(row_user['OperatingSystems']),
                int(row_user['Browser']),
                int(row_user['Region']),
                int(row_user['TrafficType']),
                visitors[row_user['VisitorType']],
                bools[row_user['Weekend']]
            ])

            labels.append(1 if row_user["Revenue"] == "TRUE" else 0)

    # Return data only when successful
    if len(evidence) == len(labels):
        return evidence, labels


def train_model(evidence, labels):
    """
    Given a list of evidence lists and a list of labels, return a
    fitted k-nearest neighbor model (k=1) trained on the data.
    """
    model = KNeighborsClassifier(n_neighbors=1)
    model.fit(evidence, labels)

    return model


def evaluate(labels, predictions):
    """
    Given a list of actual labels and a list of predicted labels,
    return a tuple (sensitivity, specificty).
    Assume each label is either a 1 (positive) or 0 (negative).
    `sensitivity` should be a floating-point value from 0 to 1
    representing the "true positive rate": the proportion of
    actual positive labels that were accurately identified.
    `specificity` should be a floating-point value from 0 to 1
    representing the "true negative rate": the proportion of
    actual negative labels that were accurately identified.
    """

    # total size
    size = len(labels)
    # initiating neg, pos, true neg and true pos values
    n = 0
    p = 0
    tp = 0
    tn = 0

    for i in range(len(labels)):
        # if negative
        if labels[i] == 0:
            n += 1
            # if the actual label matches the predicted label
            if labels[i] == predictions[i]:
                tn += 1
        # if positive
        elif labels[i] == 1:
            p += 1
            if labels[i] == predictions[i]:
                tp += 1

    sensitivity = tp / p
    specificity = tn / n

    return sensitivity, specificity


if __name__ == "__main__":
    main()

```
