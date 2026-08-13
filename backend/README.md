# Backend Infrastructure & Code

This directory contains the serverless backend code and state machine definitions for the Pet Cuddle-O-Tron application.

## 📁 Directory Contents

* **`lambda/`**: Python scripts for the API processing Lambda and the SES email dispatch Lambda (`email_remainder_lambda.py`).
* **`step-functions/`**: Amazon States Language (ASL) JSON definition (`petcuddleotron.asl.json`) configuring the Step Functions workflow and dynamic `Wait` state.
