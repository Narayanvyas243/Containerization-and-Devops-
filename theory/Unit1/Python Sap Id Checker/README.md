# SAP ID Checker – Python Docker Application

This project is a simple Python application that validates a user’s SAP ID.
The application is containerized using Docker to demonstrate automation, portability, and reproducible builds.

The user enters a SAP ID at runtime and the program verifies whether it is valid.

---

## Objective

- Learn Dockerfile basics
- Containerize a Python application
- Run interactive programs inside Docker
- Understand docker build and docker run commands

---

## Project Structure

```
sapid-checker/
├── app.py
├── Dockerfile
└── README.md
```

---

## Python Program (app.py)

Example:

```
user_sapid = input("Enter your SAP ID: ")

if user_sapid == "500122747":
    print("Valid SAP ID")
else:
    print("Invalid SAP ID")
```

---

## Dockerfile

```
FROM python:3.10-slim

WORKDIR /app

COPY app.py .

CMD ["python", "app.py"]
```

---

## Build Docker Image

Run inside project folder:

```
docker build -t sapid-checker:500122747 .
```

Explanation:

- -t → image name and tag
- . → current directory (build context)

---

## Verify Image

```
docker images
```

You should see:

```
sapid-checker   500122747
```

---

## Run Container (Important)

Since the program uses input(), run in interactive mode:

```
docker run -it sapid-checker:500122747
```

---

## Sample Output

```
Enter your SAP ID: 500122747
Valid SAP ID
```

---

## Why -it is Required

The program takes keyboard input using input().

Docker containers run in non-interactive mode by default, so input() causes:

```
EOFError: EOF when reading a line
```

Using:

```
docker run -it image_name
```

enables:

- -i → interactive input
- -t → terminal access

---

## Commands Summary

Build image:

```
docker build -t sapid-checker:500122747 .
```

Run container:

```
docker run -it sapid-checker:500122747
```

List images:

```
docker images
```

Remove container:

```
docker rm container_id
```

Remove image:

```
docker rmi sapid-checker:500122747
```

---

## Concepts Learned

- Dockerfile creation
- FROM instruction
- WORKDIR instruction
- COPY instruction
- CMD instruction
- Building images
- Running containers
- Interactive containers
- Python inside Docker

---

## Key Takeaway

Docker allows packaging Python applications with all dependencies, ensuring they run consistently on any system without manual setup.

This makes the application portable, reproducible, and automation-friendly.

---


