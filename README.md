# Amazon Kendra example search Medical Journals

## Amazon Kendra setup

Amazon Kendra requires setting up an index that will provide an endpoint for performing queries. First we select *Create Index*

![image](./images/createindex.png)

We then provide an index name, description (optional) and role that has cloudwatch / S3 access. Selecting an existing role here.

![image](./images/createindex2.png)

For Access Control settings, we selected *No*

![image](./images/createindex3.png)

For Provisioning editions settings, we selected *Developer*

![image](./images/createindex4.png)

Amazon Kendra will then create an index which will take a few minutes.

![image](./images/createindex5.png)

Once complete, the index will show *Created*.

![image](./images/createindex6.png)

The next step is to create one or more data sources for the index. The S3 data connector will be used.

![image](./images/createds.png)

Under Define attributes, a data source name is created.

![image](./images/createds2.png)

Under Configure settings, the S3 bucket that will be used is identified.

![image](./images/createds3.png)

Under Review and create, we can select Create. 

![image](./images/createds4.png)

The data source summary will show when it is completed. 

![image](./images/createds5.png)

Selecting Sync Now to have Amazon Kendra Syncing-crawl and Syncing-index. 

![image](./images/createds6.png)

In the first attempt, S3 access has not been specified in the role causing a failure. 

![image](./images/createds7.png)

Accessing the role and updating its privileges to allow S3 access is performed by attaching a managed policy. 

![image](./images/iam.png)

Within the Amazon Kendra console, we select the Sync Now button to try again.

![image](./images/createds8.png)

This will restart the Syncing-crawl effort.

![image](./images/createds9.png)

With the update IAM role, the process complete successfully.

![image](./images/createds10.png)

Selection of the Search Console in Amazon Kendra provides the following interface.

![image](./images/kendra.png)

Entering HPI (history of present illness) into the Query box correctly selects a PDF with the appropriate information.

![image](./images/kendra2.png)

## AWS CLI invocation

Once the Amazon Kendra index and data source has been created and documents crawled/scanned, it is time to invoke. This can be done via the Search mechanism directly in Amazon Kendra or 
through the use of the AWS CLI / APIs. 

The following CLI will query Amazon Kendra for the indicies that exist. We need to be able to get the index id in order to perform a Natural Language Query.

```
$ aws kendra list-indices --profile nvirginia
{ 
    "IndexConfigurationSummaryItems": [
        {
            "Name": "wound-index",
            "Id": "3a9636ff-f2fb-4006-ae1b-6cc6a7525b35",
            "Edition": "DEVELOPER_EDITION",
            "CreatedAt": "2020-11-07T19:05:52.449000-05:00",
            "UpdatedAt": "2020-11-07T19:05:52.449000-05:00",
            "Status": "ACTIVE"
        }
    ]
}
```

Once we have the index id, we can use the following command to perform a query and then examine the results. 

```
$ aws kendra query --index-id 3a9636ff-f2fb-4006-ae1b-6cc6a7525b35 --query-text "patient behaviors" --profile nvirginia
```

The results returned look like the following showing what is available based on the query. 

```
{
    "QueryId": "dc2b9c29-1c51-44f7-ad70-0470b2c2a2f4",
    "ResultItems": [
        {
            "Id": "dc2b9c29-1c51-44f7-ad70-0470b2c2a2f4-1d691efa-1673-424c-a175-3bd94dd4c57c",
            "Type": "ANSWER",
            "AdditionalAttributes": [
                {
                    "Key": "AnswerText",
                    "ValueType": "TEXT_WITH_HIGHLIGHTS_VALUE",
                    "Value": {
                        "TextWithHighlightsValue": {
                            "Text": "Don't document a skin tear, moisture-associated skin damage, a venous ulcer, an arterial ulcer, or a wound with any other\netiology as a pressure injury.\n\n\nWound Documentation Tip #6: Patient Behaviors\n\n\nDo describe in the medical record behaviors of patients who are non-adherent (non-compliant) with the plan of care.\nDocument conversations, plans to address the behaviors, educational interventions, etc. \nDon't be judgmental about a patient's non-adherence (non-compliance), and don't just continue \"business as usual.\"",
                            "Highlights": [
                                {
                                    "BeginOffset": 237,
                                    "EndOffset": 317,
                                    "TopAnswer": false
                                },
                                {
                                    "BeginOffset": 183,
                                    "EndOffset": 317,
                                    "TopAnswer": false
                                },
                                {
                                    "BeginOffset": 250,
                                    "EndOffset": 317,
                                    "TopAnswer": false
                                },
                                {
                                    "BeginOffset": 183,
                                    "EndOffset": 190,
                                    "TopAnswer": false
                                },
                                {
                                    "BeginOffset": 191,
                                    "EndOffset": 200,
                                    "TopAnswer": false
                                },
                                {
                                    "BeginOffset": 237,
                                    "EndOffset": 246,
                                    "TopAnswer": false
                                },
                                {
                                    "BeginOffset": 250,
                                    "EndOffset": 258,
                                    "TopAnswer": false
                                },
                                {
                                    "BeginOffset": 364,
                                    "EndOffset": 373,
                                    "TopAnswer": false
                                },
                                {
                                    "BeginOffset": 436,
                                    "EndOffset": 443,
                                    "TopAnswer": false
                                }
                            ]
                        }
                    }
                }
            ],
            "DocumentId": "s3://amazon-kendra-bucket/Wound Documentation Dos & Don'ts_ 10 Tips for Success.pdf",
            "DocumentTitle": {
                "Text": "Wound Documentation Dos & Don'ts_ 10 Tips for Success",
                "Highlights": []
            },
            "DocumentExcerpt": {
                "Text": "Don't document a skin tear, moisture-associated skin damage, a venous ulcer, an arterial ulcer, or a wound with any other\netiology as a pressure injury.\n\n\nWound Documentation Tip #6: Patient Behaviors\n\n\nDo describe in the medical record behaviors of patients who are non-adherent (non-compliant) with",
                "Highlights": [
                    {
                        "BeginOffset": 0,
                        "EndOffset": 300,
                        "TopAnswer": false
                    }
                ]
            },
            "DocumentURI": "https://s3.us-east-1.amazonaws.com/amazon-kendra-bucket/Wound Documentation Dos & Don'ts_ 10 Tips for Success.pdf",
            "DocumentAttributes": [
                {
                    "Key": "_excerpt_page_number",
                    "Value": {
                        "LongValue": 2
                    }
                }
            ],
            "ScoreAttributes": {
                "ScoreConfidence": "HIGH"
            }
        },
        {
            "Id": "dc2b9c29-1c51-44f7-ad70-0470b2c2a2f4-01e7cd5d-a0b3-4ed5-bf32-0552dc80e501",
            "Type": "DOCUMENT",
            "AdditionalAttributes": [],
            "DocumentId": "s3://amazon-kendra-bucket/Wound Documentation Dos & Don'ts_ 10 Tips for Success.pdf",
            "DocumentTitle": {
                "Text": "Wound Documentation Dos & Don'ts_ 10 Tips for Success",
                "Highlights": []
            },
            "DocumentExcerpt": {
                "Text": "...Tip #6: Patient Behaviors\n\n\nDo describe in the medical record behaviors of patients who are non-adherent (non-compliant) with the plan of care.\nDocument conversations, plans to address the behaviors, educational interventions, etc. \nDon't be judgmental about a patient's non-adherence...",
                "Highlights": [
                    {
                        "BeginOffset": 11,
                        "EndOffset": 18,
                        "TopAnswer": false
                    },
                    {
                        "BeginOffset": 19,
                        "EndOffset": 28,
                        "TopAnswer": false
                    },
                    {
                        "BeginOffset": 65,
                        "EndOffset": 74,
                        "TopAnswer": false
                    },
                    {
                        "BeginOffset": 78,
                        "EndOffset": 86,
                        "TopAnswer": false
                    },
                    {
                        "BeginOffset": 192,
                        "EndOffset": 201,
                        "TopAnswer": false
                    },
                    {
                        "BeginOffset": 264,
                        "EndOffset": 271,
                        "TopAnswer": false
                    }
                ]
            },
            "DocumentURI": "https://s3.us-east-1.amazonaws.com/amazon-kendra-bucket/Wound Documentation Dos & Don'ts_ 10 Tips for Success.pdf",
            "DocumentAttributes": [
                {
                    "Key": "_source_uri",
                    "Value": {
                        "StringValue": "https://s3.us-east-1.amazonaws.com/amazon-kendra-bucket/Wound Documentation Dos & Don'ts_ 10 Tips for Success.pdf"
                    }
                },
                {
                    "Key": "_excerpt_page_number",
                    "Value": {
                        "LongValue": 2
                    }
                }
            ],
            "ScoreAttributes": {
                "ScoreConfidence": "LOW"
            }
        },
        {
            "Id": "dc2b9c29-1c51-44f7-ad70-0470b2c2a2f4-6420e9dd-77df-4302-8a4f-d8917879551c",
            "Type": "DOCUMENT",
            "AdditionalAttributes": [],
            "DocumentId": "s3://amazon-kendra-bucket/Wound_Care_Medical_Record_Documentation.9.pdf",
            "DocumentTitle": {
                "Text": "Wound_Care_Medical_Record_Documentation.9",
                "Highlights": []
            },
            "DocumentExcerpt": {
                "Text": "...is the first step toward complete docu-\n\n\nmentation for the skin and wound care patient. The chief com-\n\n\nplaint bridges the reason for the patient’s visit and the detailed\n\n\nhistory and physical data captured by the practitioner about\n\n\nthe medical necessity for the visit. The clinician...",
                "Highlights": [
                    {
                        "BeginOffset": 83,
                        "EndOffset": 90,
                        "TopAnswer": false
                    },
                    {
                        "BeginOffset": 143,
                        "EndOffset": 150,
                        "TopAnswer": false
                    }
                ]
            },
            "DocumentURI": "https://s3.us-east-1.amazonaws.com/amazon-kendra-bucket/Wound_Care_Medical_Record_Documentation.9.pdf",
            "DocumentAttributes": [
                {
                    "Key": "_source_uri",
                    "Value": {
                        "StringValue": "https://s3.us-east-1.amazonaws.com/amazon-kendra-bucket/Wound_Care_Medical_Record_Documentation.9.pdf"
                    }
                },
                {
                    "Key": "_excerpt_page_number",
                    "Value": {
                        "LongValue": 1
                    }
                }
            ],
            "ScoreAttributes": {
                "ScoreConfidence": "LOW"
            }
        },
        {
            "Id": "dc2b9c29-1c51-44f7-ad70-0470b2c2a2f4-982142e9-6b2a-46a0-887a-fe442463b967",
            "Type": "DOCUMENT",
            "AdditionalAttributes": [],
            "DocumentId": "s3://amazon-kendra-bucket/Nine Wound Care Documentation Pitfalls to Avoid - WCEI Blog WCEI Blog.pdf",
            "DocumentTitle": {
                "Text": "Nine Wound Care Documentation Pitfalls to Avoid - WCEI Blog WCEI Blog",
                "Highlights": []
            },
            "DocumentExcerpt": {
                "Text": "...subject in wound litigation because most patients do tend to lose body weight\nas they become ill and certainly as they reach end of life. However, it is difficult to defend weight losses that\nseem implausible. For example, a recent case showed the patient weighed 195 pounds upon admission to the...",
                "Highlights": [
                    {
                        "BeginOffset": 44,
                        "EndOffset": 52,
                        "TopAnswer": false
                    },
                    {
                        "BeginOffset": 251,
                        "EndOffset": 258,
                        "TopAnswer": false
                    }
                ]
            },
            "DocumentURI": "https://s3.us-east-1.amazonaws.com/amazon-kendra-bucket/Nine Wound Care Documentation Pitfalls to Avoid - WCEI Blog WCEI Blog.pdf",
            "DocumentAttributes": [
                {
                    "Key": "_source_uri",
                    "Value": {
                        "StringValue": "https://s3.us-east-1.amazonaws.com/amazon-kendra-bucket/Nine Wound Care Documentation Pitfalls to Avoid - WCEI Blog WCEI Blog.pdf"
                    }
                },
                {
                    "Key": "_excerpt_page_number",
                    "Value": {
                        "LongValue": 2
                    }
                }
            ],
            "ScoreAttributes": {
                "ScoreConfidence": "LOW"
            }
        }
    ],
    "FacetResults": [],
    "TotalNumberOfResults": 4
}
```
