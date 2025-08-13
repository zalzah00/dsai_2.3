# Assignment

## Brief

Write the Python codes for the following questions.

## Instructions

Paste the answer as Python in the answer code section below each question.

### Question 1

Question: Implement a simple Thrift server and client that defines a `Student` struct with fields `name` (string), `age` (integer), and `courses` (list of strings). Include a service `School` with a method `enrollCourse` that takes a `Student` record and a course name, adds the course to the student's course list, and returns the updated `Student` record.

Answer:

```python
# Thrift schema (student.thrift)
struct Student {
  1: required string name,
  2: required i32 age,
  3: required list<string> courses
}

service School {
  Student enrollCourse(1: required Student student, 2: required string courseName)
}
# Thrift server (student_server.py)
import thriftpy2
student_thrift = thriftpy2.load("student.thrift", module_name="student_thrift")

from thriftpy2.rpc import make_server

class SchoolHandler:
    def enrollCourse(self, student, courseName):
        print(f"Server: Enrolling {student.name} in course: {courseName}")
        student.courses.append(courseName)
        print(f"Server: Updated student courses: {student.courses}")
        return student

server = make_server(student_thrift.School, SchoolHandler(), client_timeout=None)
print('Starting server...')
server.serve()

# Thrift client (student_client.py)
import thriftpy2
student_thrift = thriftpy2.load("student.thrift", module_name="student_thrift")

from thriftpy2.rpc import make_client

# Connect to the server
client = make_client(student_thrift.School)

# Create an initial Student record
alice = student_thrift.Student(
    name="Alice", 
    age=15, 
    courses=["Science", "English"]
)

# Print initial courses
print(alice.courses)

# Enroll in a new course and get the updated record
alice = client.enrollCourse(alice, "Maths")

# Print updated courses
print(alice.courses)
```

### Question 2

Question: Implement a simple Protocol Buffers server and client that defines a `Book` message with fields `title` (string), `author` (string), and `page_count` (integer). Include a service `Library` with a method `checkoutBook` that takes a `Book` message and returns the same `Book` message.

Answer:

```python
# Protobuf schema (book.proto)

# Protobuf server (book_server.py)
from concurrent import futures
import grpc
import library_pb2_grpc

class LibraryServicer(library_pb2_grpc.LibraryServicer):
    def checkoutBook(self, request, context):
        print(f"Server received checkout request for: {request.title}")
        return request

server = grpc.server(futures.ThreadPoolExecutor(max_workers=2))
library_pb2_grpc.add_LibraryServicer_to_server(LibraryServicer(), server)
server.add_insecure_port('[::]:50051')
server.start()
print("Server started on port 50051...")
server.wait_for_termination()

# Protobuf client (book_client.py)
import grpc
import library_pb2
import library_pb2_grpc

with grpc.insecure_channel('localhost:50051') as channel:
    stub = library_pb2_grpc.LibraryStub(channel)
    book_to_checkout = library_pb2.Book(title="Humpty Dumpty", author="Mother Goose", page_count=1)
    print("Client sending book for checkout...")
    response = stub.checkoutBook(book_to_checkout)
    print(f"Client received checked out book: {response.title} by {response.author}")
```

## Submission

- Submit the URL of the GitHub Repository that contains your work to NTU black board.
- Should you reference the work of your classmate(s) or online resources, give them credit by adding either the name of your classmate or URL.
