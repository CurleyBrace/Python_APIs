# Python API Projects

## Introduction
This repo will contain most projects I've created to help utilize and better understand how to create APIs using Python as the primary language. It will take advantage of micro-frameworks, and Web Services such as Flask, SQL Alchemy and more. As I dive further into each of them and learn more about how to properly take advantage of everything they have to offer I will continue to update this Repo and Readme with information and examples from each major project I've done, so please check back often if it interests you. 

Below are descriptions of projects I've worked on along with code snippets and navigation links for those that I am able to display using Github. Some of these projects may be listed multiple times depending on the examples I've used.


## API Project Examples
* Bookstore API
  * [Creating the API Calls to edit data](#book-api-calls)
  * [Creating the Model](#book-model-code)
* Online Video API
  * [Creating the API Calls to edit data](#video-api-calls)
  * [Creating the Model](#video-model-layout)


### [Book API Calls](https://github.com/CurleyT/Python_APIs/blob/main/Flask_SQLAlchemy/Book_API/main.py)
This program hosts a server and will allow you to make calls to alter the books stored in a database using most HTTP methods available. Upon request will return responses, either status codes, boolean feedback, or will return a JSON object in the body. This project is the one I am currently updating most frequently, so please check back later for more! Below is the POST call method used as an example.
```
#POST /books
@app.route('/books', methods=['POST'])
def add_book():
    request_data = request.get_json()
    if(validBookObject(request_data)):
        Book.add_book(request_data['name'], request_data['price'], request_data['isbn'])
        response = Response("", 201, mimetype='application/json')
        response.headers['Location'] = "/books/" + str(request_data['isbn'])
        return response
    else:
        invalidBookObjectErrorMsg = {
            "error": "Invalid book object passed in request",
            "helpString": "Data passed in similar to this {'name': 'bookname', 'price': 7.99, 'isbn': 1234567890}"
        }
        response = Response(json.dumps(invalidBookObjectErrorMsg), status=400, mimetype='application/json')
        return response
```

### [Book Model Code](https://github.com/CurleyT/Python_APIs/blob/main/Flask_SQLAlchemy/Book_API/BookModel.py)
Unlike in my video api project, I've actually taken the model out from my main server object and created a seperate class to allow for better scalability and abstraction. Below is just a small snippet of code showing the basic model we've created and utilized in this program. There is more to it than just this, so feel free to check out the rest of the code!
```
    def __repr__(self):
        book_object = {
            'name': self.name,
            'price': self.price,
            'isbn': self.isbn
        }
        return json.dumps(book_object)
```


### [Video API Calls](https://github.com/CurleyT/Python_APIs/blob/main/Flask_SQLAlchemy/Video_API/main.py)
This is my first set of code utilizing Flask and SQL Alchemy and will take in information related to videos posted online and then store it in a local DB for referencing later. It utilizes different resource fields and should give proper errors or exceptions if things do not perform as expected. Below is an example of the PATCH method created for this project. There are some more efficiencies to be done when working on these calls and parsing through models though, so please look forward to future changes!
```
    @marshal_with(resource_fields)
    def patch(self, video_id):
        args = video_update_args.parse_args()
        result = VideoModel.query.filter_by(id=video_id).first()
        if not result:
            abort(404, message="Could not find video with that ID, cannot update..")
            
        if args['name']:
            result.name = args['name']
        if args['views']:
            result.views = args['views']
        if args['likes']:
            result.likes = args['likes']

        db.session.commit()
        return result
```

### [Video Model Layout](https://github.com/CurleyT/Python_APIs/blob/main/Flask_SQLAlchemy/Video_API/main.py)
This is a basic model created to store information related to videos posted on an online streaming website. At the moment this exists within our current main.py file and could be removed to store inside of it's own file later on. It also utilizes an F statement to return information in a concise manner after creating an instance of itsself. 
```
class VideoModel(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(100), nullable=False)
    views = db.Column(db.Integer, nullable=False)
    likes = db.Column(db.Integer, nullable=False)

    def __repr__(self):
        return f"Video(name = {name}, views = {views}, likes = {likes})"
```
