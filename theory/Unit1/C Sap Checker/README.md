# C SAP ID Checker – Dockerized Application

This project is a simple SAP ID validation program written in C and containerized using Docker.

The program asks the user to enter a SAP ID and checks whether it matches the predefined valid SAP ID.

The application is built inside a Docker container using GCC and runs interactively.

---

## Objective

- Write a C program for SAP ID validation
- Compile C code using GCC
- Create Dockerfile for C applications
- Build Docker image
- Run interactive container

---

## Project Structure

```
C-docker-app/
├── main.c
├── Dockerfile
└── README.md
```
![](Screenshot%202026-02-09%20170833.png)
---

## C Program (main.c)

```
#include <stdio.h>

int main() {
    int sap;

    printf("Enter your SAP ID: ");
    scanf("%d", &sap);

    if(sap == 500122747)
        printf("Matched\n");
    else
        printf("Not Matched\n");

    return 0;
}
```
![](Screenshot%202026-02-09%20170852.png)
---

## Dockerfile

```
FROM gcc:latest

WORKDIR /app

COPY main.c .

RUN gcc main.c -o sapid_checker

CMD ["./sapid_checker"]
```
![](Screenshot%202026-02-09%20172004.png)
---

## Build Docker Image

Open terminal inside project folder:

```
docker build -t c-sapid-checker:1.0 .
```

Explanation:

- build → create image
- -t → tag name
- c-sapid-checker → image name
- 1.0 → version
- . → current directory (build context)

---

## Verify Image

```
docker images
```

---

## Run Container (Important)

Since the program takes input using scanf(), run with interactive mode:

```
docker run -it c-sapid-checker:1.0
```

---

## Sample Output

Valid case:

```
Enter your SAP ID: 500122747
Matched
```

Invalid case:

```
Enter your SAP ID: 123456
Not Matched
```

---

## Why -it is Required

The program uses scanf() for keyboard input.

Docker containers run in non-interactive mode by default.  
Without -it, input will not work correctly.

Flags:

- -i → interactive input
- -t → terminal access

So always run:

```
docker run -it image_name
```

---

## Commands Summary

Build:

```
docker build -t c-sapid-checker:1.0 .
```

Run:

```
docker run -it c-sapid-checker:1.0
```
![](Screenshot%202026-02-09%20171057.png)

List images:

```
docker images
```

Remove image:

```
docker rmi c-sapid-checker:1.0
```

---

## Concepts Used

- C Programming
- GCC Compiler
- Dockerfile
- FROM instruction
- WORKDIR instruction
- COPY instruction
- RUN instruction
- CMD instruction
- Docker build
- Docker run

---

## Key Takeaway

Docker allows compiling and running C programs inside containers without installing GCC locally.  
This ensures portability, reproducibility, and consistent execution across systems.

---
