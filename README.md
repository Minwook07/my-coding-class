# Exercise 4 (13-Feb-2026)
## JavaScript Exercise 4
```javascript
let myForm = document.getElementById("my-form");
let tableBody = document.getElementById("tableBody");
let title = document.getElementById("title");
let desc = document.getElementById("description");
let published = document.getElementById("published");
let titleForm = document.querySelector(".title-form");
let btnSave = document.querySelector(".btn");
let isEdit = null;

// 1. Function សម្រាប់ទាញទិន្នន័យពី Server មកបង្ហាញក្នុង Table
function displayBook() {
    let published = document.getElementById("publishedCheck");
    let indexBook = "https://api-crud-yrvv.onrender.com/api/tutorials";
    let publishedBook = "https://api-crud-yrvv.onrender.com/api/tutorials/published";

    let endpoint = published.checked ? publishedBook : indexBook;

    fetch(endpoint)
        .then(res => res.json())
        .then(data => {
            console.log(data);
            tableBody.innerHTML = "";
            data.forEach(item => {
                tableBody.innerHTML +=
                    `
                <tr>
                    <td>${item.id}</td>
                    <td>${item.title}</td>
                    <td>${item.description}</td>
                    <td class="${item.published ? "text-success" : "text-danger"}">
                        ${item.published ? "Published" : "Draft"}
                    </td>
                    <td>
                        <span>
                            <i class="bi bi-pencil-square text-primary mx-1 fs-5 cursor-pointer" onclick="editBook(${item.id})"></i>
                        </span>
                        <span>
                            <i class="bi bi-trash3 text-danger mx-1 fs-5 cursor-pointer"></i>
                        </span>
                    </td>
                </tr>
            `
            })
        })
}

// 2. Function សម្រាប់បង្កើតទិន្នន័យថ្មី (POST)
function addBook() {
    fetch("https://api-crud-yrvv.onrender.com/api/tutorials", {
        method: "POST",
        headers: {
            "Content-Type": "application/json"
        },
        body: JSON.stringify({
            title: title.value,
            description: desc.value,
            published: published.checked ? 1 : 0
        })
    })
        .then(res => res.json())
        .then(data => {
            myForm.reset();
            displayBook();
            console.log(data);
        })
}

// 3. Function ពេលចុចប៊ូតុង Edit: ទាញទិន្នន័យចាស់មកដាក់ក្នុង Form
function editBook(id) {
    isEdit = id;
    titleForm.innerHTML = "Update Item";
    btnSave.innerHTML = "Save Change";

    fetch(`https://api-crud-yrvv.onrender.com/api/tutorials/${id}`, {
        method: "GET"
    })
        .then(res => res.json())
        .then(data => {
            title.value = data.title;
            desc.value = data.description;
            published.checked = data.published;
        })
}

// 4. Function សម្រាប់រក្សាទុកការកែប្រែ (PUT)
function saveEdit(id) {
    fetch(`https://api-crud-yrvv.onrender.com/api/tutorials/${id}`, {
        method: "PUT",
        headers: {
            "Content-Type": "application/json"
        },
        body: JSON.stringify({
            title: title.value,
            description: desc.value,
            published: published.checked ? 1 : 0
        })
    })
    .then(res => res.json())
    .then(data => {
        isEdit = null; // ដាក់ isEdit null ឱ្យវាបង្កើតថ្មី បើមិនដូច្នេះទេ ពេលយើងចង់ Add ថ្មី កម្មវិធីនឹងច្រឡំថាទៅ Edit របស់ចាស់ដដែល
        titleForm.innerHTML = "Create New Item";
        btnSave.innerHTML = "Submit";
        myForm.reset();
        displayBook();
        console.log(data);
    })
}

// 5. ចាប់ព្រឹត្តិការណ៍ពេល User ចុច Submit Form
myForm.addEventListener("submit", function (e) {
    e.preventDefault();
    if(isEdit) {
        saveEdit(isEdit);
    }
    else {
        addBook();
    }
})

// Function ពេលចុចបង្ហាញតែ published book
function doCheck() {
    displayBook();
}

displayBook();
```

## HTML Exercise 4
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Form with Table Below</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.11.3/font/bootstrap-icons.css" rel="stylesheet">
</head>

<body>
    <div class="container mt-5">
        <h2 class="title-form">Create New Item</h2>

        <!-- Form -->
        <form id="my-form">
            <!-- Title -->
            <div class="mb-3">
                <label for="title" class="form-label fw-bold">Title</label>
                <input type="text" class="form-control" id="title" placeholder="Enter title">
                <span id="error-title" class="text-danger"></span>
            </div>

            <!-- Description -->
            <div class="mb-3">
                <label for="description" class="form-label fw-bold">Description</label>
                <textarea class="form-control" id="description" rows="3" placeholder="Enter description"></textarea>
                <span id="error-description" class="text-danger"></span>

            </div>

            <!-- Published -->
            <div class="form-check mb-3">
                <input class="form-check-input" type="checkbox" id="published">
                <label class="form-check-label" for="published">Published</label>
            </div>

            <!-- Submit -->
            <button type="submit" class="btn btn-primary">Submit</button>
        </form>

        <h3 class="text-center mb-4">Tutorial List</h3>

        <!-- Checkbox -->
        <div class="form-check mb-3">
            <input class="form-check-input" type="checkbox" id="publishedCheck" onclick="doCheck()">
            <label class="form-check-label" for="publishedCheck">
                Show Only Published
            </label>
        </div>

        <!-- Table -->
        <table class="table table-bordered table-hover text-center align-middle">
            <thead class="table-dark">
                <tr>
                    <th>ID</th>
                    <th>Title</th>
                    <th>Description</th>
                    <th>Status</th>
                    <th>Action</th>

                </tr>
            </thead>
            <tbody id="tableBody">
                
            </tbody>
        </table>
    </div>


    <!-- Bootstrap JS (optional) -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    <script src="script.js"></script>
</body>

</html>
```

# Practice with Event "onkeydown && onkeyup"
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Keyboard Event Practice</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.8/css/bootstrap.min.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Kantumruy+Pro:wght@400;700&display=swap" rel="stylesheet">
    <style>
        body {
            background-color: #f8f9fa;
            font-family: "Kantumruy Pro", sans-serif;
            padding-top: 50px;
        }

        .card {
            border-radius: 15px;
            border: none;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
        }

        .display-counter {
            font-size: 1.2rem;
            font-weight: bold;
            color: #0d6efd;
            background: #e7f1ff;
            padding: 10px;
            border-radius: 10px;
            margin-top: 15px;
        }
    </style>
</head>

<body>
    <div class="container">
        <div class="row g-4 justify-content-center">
            <h2 class="text-center">ការអនុវត្ត Keyboard Event</h2>

            <div class="col-md-5">
                <div class="card p-4 h-100 text-center border-top border-primary border-5">
                    <div class="d-flex justify-content-center align-items-center mb-5">
                        <img src="img/typing-board.png" class="w-50" alt="">
                    </div>
                    <input type="text" class="form-control shadow-none mb-3" id="input1"
                        placeholder="សូមសាកល្បងវាយអក្សរ..." onkeydown="handleCount1()" onfocus="handleStyle()"
                        onblur="handleStyle2()">
                    <div class="display-counter" id="display1">ចំនួនចុច: 0</div>
                    <p class="small text-danger mt-3">ចំណាំ: ពេលចុច key ជាប់ វានឹងបន្ត count រហូត</p>
                </div>
            </div>

            <div class="col-md-5">
                <div class="card p-4 h-100 text-center border-top border-success border-5">
                    <div class="d-flex justify-content-center align-items-center mb-5">
                        <img src="img/laptop.png" class="w-50" alt="">
                    </div>
                    <input type="text" class="form-control shadow-none mb-3" id="input2"
                        placeholder="សូមសាកល្បងវាយអក្សរ..." onkeyup="handleCount2()" onfocus="handleStyle3()">
                    <div class="display-counter text-success" id="display2" style="background: #e8f5e9;">ចំនួនចុច: 0
                    </div>
                    <p class="small text-success mt-3">ចំណាំ: ទាល់តែលែងដៃ ទើបវា count បន្ថែម</p>
                </div>
            </div>

        </div>
    </div>

    <script>
        let input1 = document.getElementById('input1');
        let input2 = document.getElementById('input2');

        let display1 = document.getElementById('display1');
        let display2 = document.getElementById('display2');
        let total = 0;
        var kkk = 1;
        var kkk = 333;

        function handleCount1() {
            total = total + 1;
            // total += 1;
            // total++;
            display1.textContent = total;

        }

        function handleStyle() {
            input1.classList.add('display-counter');
        }

        function handleStyle2() {
            input1.classList.remove('display-counter');
        }

        function handleCount2() {
            total += 1;
            display2.innerText = total;
        }

        function handleStyle3() {
            input2.classList.add('display-counter');
        }
    </script>
</body>

</html>
```

# Practice with Event
## JavaScript
```javascript
    <script>
        let username = document.getElementById('username');
        let course = document.getElementById('courseSelect');
        let showCourse = document.getElementById('validSuccess');
        let showSuccess = document.getElementById('successAlert');

        function usrName() {
            username.classList.add('text-warning');
        }

        function usrNameOut() {
            username.classList.remove('text-warning');
        }

        function usrNameInput() {
            if (username.value.trim() !== "" && username.value.length >= 6) {
                username.classList.add('is-valid');
                username.classList.remove('is-invalid');
            } else {
                username.classList.add('is-invalid');
                username.classList.remove('is-valid');
            }
        }

        function selectCourse() {
            if (course.value === "0") {
                course.classList.add('is-invalid');
                course.classList.remove('is-valid');
            } else {
                course.classList.add('is-valid');
                course.classList.remove('is-invalid');
                showCourse.innerHTML = course.value;
            }

        }

        function registrationForm(e) {
            e.preventDefault();

            let isValid = true;

            if (username.value.trim() !== "" && username.value.length >= 6) {
                username.classList.add('is-valid');
                username.classList.remove('is-invalid');
            } else {
                username.classList.add('is-invalid');
                username.classList.remove('is-valid');
                isValid = false;
            }

            if (course.value === "0") {
                course.classList.add('is-invalid');
                course.classList.remove('is-valid');
                isValid = false;
            } else {
                course.classList.add('is-valid');
                course.classList.remove('is-invalid');
                showCourse.innerHTML = course.value;
            }

            if (isValid) {
                showSuccess.classList.remove('d-none');
            }
        }
    </script>
```

## HTML
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.8/css/bootstrap.min.css">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Content:wght@400;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: "Content", system-ui;
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            background-color: #f8f9fa;
        }

        .card {
            width: 100%;
            max-width: 500px;
        }
    </style>
</head>

<body>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-6 col-lg-4">
                <div class="card p-4 shadow border-0">
                    <div id="successAlert" class="alert alert-success alert-dismissible fade show d-none" role="alert">
                        <strong>ជោគជ័យ!</strong> ការចុះឈ្មោះរបស់អ្នកបានសម្រេច!
                        <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
                    </div>
                    <h2 class="mb-4 text-center h4">ចុះឈ្មោះចូលរៀន</h2>
                    <form onsubmit="registrationForm(event)">
                        <div class="mb-3">
                            <label class="form-label">ឈ្មោះអ្នកប្រើ</label>
                            <input type="text" id="username" onfocus="usrName()" onblur="usrNameOut()"
                                onkeyup="usrNameInput()" class="form-control shadow-none" placeholder="បញ្ចូលឈ្មោះ...">
                            <div class="valid-feedback">
                                ឈ្មោះប្រើប្រាស់បាន!
                            </div>
                            <div class="invalid-feedback">
                                សូមបញ្ចូលឈ្មោះរបស់អ្នកយ៉ាងតិច៦តួអក្សរ
                            </div>
                        </div>

                        <div class="mb-3">
                            <label class="form-label">ជ្រើសរើសវគ្គសិក្សា</label>
                            <select id="courseSelect" onchange="selectCourse()" class="form-select shadow-none">
                                <option value="0">--- ជ្រើសរើសវគ្គសិក្សា ---</option>
                                <option value="50">JavaScript ($50)</option>
                                <option value="80">PHP Laravel ($80)</option>
                                <option value="100">UX/UI Design ($100)</option>
                            </select>
                            <div class="valid-feedback" id="validSuccess"></div>
                            <div class="invalid-feedback">
                                សូមជ្រើសរើសវគ្គសិក្សាសិន
                            </div>
                        </div>

                        <button type="submit" class="btn btn-success w-100">ចុះឈ្មោះឥឡូវនេះ</button>
                    </form>
                </div>
            </div>
        </div>
    </div>
</body>

</html>
```

# Lesson "Event" with Example

## "onchange"
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.8/css/bootstrap.min.css">
</head>

<body>
    <div class="col-md-4 m-3">
        <select class="form-select" onchange="valueSelect()" id="formSelect"
            aria-label="Default select example">
            <option selected>Open this select menu</option>
            <option value="1">One</option>
            <option value="2">Two</option>
            <option value="3">Three</option>
        </select>
        <p id="showResult"></p>
    </div>
    <script>
        let show = document.getElementById('showResult');
        function valueSelect() {
            show.innerHTML = frmSelect.value;
        }
    </script>
</body>

</html>
```

## "onfocus && onblur" Use without Keyword "this", Use Id DOM
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.8/css/bootstrap.min.css">
</head>

<body>
    <div class="container">
        <div class="row">
            <div class="col-md-4 m-3">
                <label for="validationCustom01" class="form-label">Name</label>
                <input type="text" class="form-control shadow-none" id="name" onfocus="nameInput()"
                    onblur="nameInput2()" required>
            </div>
        </div>
    </div>
    <script>
        // Use DOM by id
        let nameIn = document.getElementById('name');
    
        function nameInput() {
            // name.classList.add('bg-warning');
            nameIn.style.backgroundColor = "#000000";
        }
        
        function nameInput2() {
            nameIn.style.backgroundColor = "#ffffff";
        }
    </script>
</body>

</html>
```

## "onfocus && onblur" Use with Keyword "this"
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bootstrap/5.3.8/css/bootstrap.min.css">
</head>

<body>
    <div class="container">
        <div class="row">
            <div class="col-md-4 m-3">
                <label for="validationCustom01" class="form-label">Name</label>
                <input type="text" class="form-control shadow-none" onfocus="nameInput(this)"
                    onblur="nameInput2(this)" required>
            </div>
        </div>
    </div>
    <script>
        // Use with keyword --> this <--
        // Dont' need to dom element by id, or class 
        function nameInput(nameIn) {
            // nameIn.classList.add('bg-warning');
            nameIn.style.backgroundColor = "#000000";
        }
        
        function nameInput2(nameIn) {
            nameIn.style.backgroundColor = "#ffffff";
        }
    </script>
</body>

</html>
```
# End of Lesson "Event"

# Practice
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>DOM Exercise</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        .border-male {
            border-left: 6px solid blue;
        }
        .border-female {
            border-left: 6px solid orange;
        }
    </style>
</head>

<body>
    <div class="container mt-5">
        <div class="row justify-content-center">
            <div class="col-md-6">
                <div class="card shadow">
                    <div class="card-header bg-primary text-white">
                        <h4 class="mb-0">Add Student</h4>
                    </div>
                    <!-- form Create Student -->
                    <div class="card-body">
                        <form id="studentForm">

                            <!-- First Name -->
                            <div class="mb-3">
                                <label class="form-label">First Name</label>
                                <input type="text" class="form-control" id="firstName">
                                <div class="invalid-feedback">
                                    First name is required
                                </div>
                            </div>

                            <!-- Last Name -->
                            <div class="mb-3">
                                <label class="form-label">Last Name</label>
                                <input type="text" class="form-control" id="lastName">
                                <div class="invalid-feedback">
                                    Last name is required
                                </div>
                            </div>

                            <!-- Age -->
                            <div class="mb-3">
                                <label class="form-label">Age</label>
                                <input type="number" class="form-control" id="age">
                                <div class="invalid-feedback">
                                    Age must be greater than 0
                                </div>
                            </div>
                            <!-- Gender -->
                            <div class="mb-3">
                                <label class="form-label">Gender</label>
                                <select class="form-select" id="gender">
                                    <option value="">Select gender</option>
                                    <option value="male">Male</option>
                                    <option value="female">Female</option>
                                </select>
                                <div class="invalid-feedback">
                                    Gender is required
                                </div>
                            </div>

                            <!-- Photo -->
                            <div class="mb-3">
                                <label class="form-label">Photo Path</label>
                                <input type="text" class="form-control" id="photo" placeholder="img/male.png"
                                    value="img/">
                                <div class="invalid-feedback">
                                    Photo is required
                                </div>
                            </div>

                            <button type="submit" class="btn btn-primary w-100">
                                Save Student
                            </button>

                        </form>
                    </div>
                </div>

            </div>
        </div>
        <!-- Display Card Student-->

        <h1 class="text-center mt-4">List of Student</h1>
        <div class="row my-5" id="studentList">
            <!-- example -->
        </div>
    </div>
</body>
<script>
    const students = [
        { firstName: "John", lastName: "Doe", age: 20, gender: "male", photo: "img/male.png" },
        { firstName: "Sokha", lastName: "Chan", age: 22, gender: "female", photo: "img/female.png" },
        { firstName: "Dara", lastName: "Kim", age: 19, gender: "male", photo: "img/male.png" },
        { firstName: "Mara", lastName: "Mey", age: 19, gender: "female", photo: "img/female.png" },
        { firstName: "Bona", lastName: "Sokhom", age: 23, gender: "male", photo: "img/male.png" }
    ]

    let studentBtn = document.getElementById('studentForm');
    let studentShow = document.getElementById('studentList');

    let firstNameValid = document.getElementById('firstName');
    let lastNameValid = document.getElementById('lastName');
    let ageValid = document.getElementById('age');
    let genderValid = document.getElementById('gender');
    let photoValid = document.getElementById('photo');

    function renderList() {
        studentShow.innerHTML = ""
        students.forEach(item => {
            studentShow.innerHTML +=
                `
            <div class="col-md-4 mb-4">
                <div class="card shadow ${item.gender.toLowerCase() === "female" ? "border-female" : "border-male"}">
                <div class="card-body text-center">
                    <img src="${item.photo}" class="img-fluid rounded-circle mb-3 w-50">
                    <h5>${item.firstName} ${item.lastName}</h5>
                    <p class="mb-1">Age: ${item.age}</p>
                    <p class="mb-0">Gender: ${item.gender}</p>
                </div>
                </div>
            </div>
        `
        })
    }

    renderList();
    studentBtn.addEventListener("submit", function (e) {
        e.preventDefault();
        let isValid = true;

        let firstNameInput = firstNameValid.value.trim();
        let lastNameInput = lastNameValid.value.trim();
        let ageInput = ageValid.value.trim();
        let genderInput = genderValid.value.trim();
        let photoInput = photoValid.value.trim();

        if (firstNameInput === "") {
            firstNameValid.classList.add('is-invalid');
            isValid = false;
        } else {
            firstNameValid.classList.remove('is-invalid');
        }

        if (lastNameInput === "") {
            isValid = false;

            lastNameValid.classList.add('is-invalid');
        } else {
            lastNameValid.classList.remove('is-invalid');
        }

        if (ageInput === "" | ageInput <= 0) {
            isValid = false;

            ageValid.classList.add('is-invalid');
        } else {
            ageValid.classList.remove('is-invalid');
        }

        if (genderInput === "") {
            isValid = false;

            genderValid.classList.add('is-invalid');
        } else {
            genderValid.classList.remove('is-invalid');
        }

        if (photoInput === "" | photoInput === "img/") {
            isValid = false;
            photoValid.classList.add('is-invalid');
        } else {
            photoValid.classList.remove('is-invalid');
        }

        if (isValid) {
            let studentNews = {
                firstName: firstNameInput,
                lastName: lastNameInput,
                age: ageInput,
                gender: genderInput,
                photo: photoInput
            }
            students.push(studentNews);
            renderList();
            studentBtn.reset();
        }
    })
</script>

</html>
```

# Homework 
<img src="homework.png" width="600" style="border-radius: 10px; border: 1px solid red;">

# Homework - HTML Code
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>User Table</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
</head>

<body>
    <div class="container mt-5">
        <h3 class="mb-3">User List</h3>

        <table class="table table-bordered table-hover align-middle">
            <thead class="table-dark">
                <tr>
                    <th>ID</th>
                    <th>First Name</th>
                    <th>Last Name</th>
                    <th>Gender</th>
                    <th>Status</th>
                </tr>
            </thead>
            <tbody id="userTable">
                <!-- Data will be inserted here -->
                <!-- <tr>
          <td>1</td>
          <td>Mey</td>
          <td>Mora</td>
          <td>Female</td>
          <td>
            <span class="badge bg-success">
              Active
            </span>
          </td>
        </tr>
        <tr>
          <td>2</td>
          <td>Sok</td>
          <td>San</td>
          <td>Male</td>
          <td>
            <span class="badge bg-secondary">
              Disabled
            </span>
          </td>
        </tr> -->
            </tbody>
        </table>
    </div>
</body>
<script src="script.js"></script>

</html>
```


# Classwork 1 - JavaScript Code

```javascript
let errorTag = document.querySelectorAll(".text-danger");
const addStudent = () => {
    event.preventDefault();
    //1. get value from form 
    let enName = document.querySelector("#enName").value.trim();
    let khName = document.querySelector("#khName").value.trim();
    let email = document.querySelector("#email").value.trim();
    let phone = document.querySelector("#phone").value.trim();
    let gender = document.querySelector("input[name='gender']:checked"); // tell javascript to look up tag <input> that have attribute name="gender"
    let subjects = document.querySelectorAll(".subject:checked");
    //2 remove message error
    errorTag.forEach(err => {
        err.innerText = ""

    });
    //3. check validation
    let isvalid = true;
    // 4. check all input
    if (enName === "") {
        isvalid = false;
        document.getElementById("enNameError").innerText = "Select English name"
    }
    if (khName === "") {
        isvalid = false;
        document.getElementById("khNameError").innerText = "Select Khmer name"
    }
    if (email === "") {
        isvalid = false
        document.getElementById("emailError").innerText = "Select Email name"
    }
    if (phone === "") {
        isvalid = false
        document.getElementById("phoneError").innerText = "Select phone name"
    }
    if (!gender) {
        isvalid = false
        document.getElementById("genderError").innerText = "Select gender"

    }
    if (subjects.length == 0) {
        isvalid = false
        document.getElementById("subjectError").innerText = "Select subject at lest one subject"

    }
    //5. check if isvalid false it will return (stop code)
    if (!isvalid) {
        return;
    }

    // 6. isvalid = true
    let subjectSelect = "";
    subjects.forEach((sub, index) => {
        if (index == 0) {
            subjectSelect = sub.value;
        } else if (index === subjects.length - 1) {
            subjectSelect += ` and ${sub.value}`;
        } else {
            subjectSelect += `, ${sub.value}`
        }

    });
    // 7. open card result
    document.querySelector("#resultCard").classList.remove("d-none")
    // 8. hide form 
    document.querySelector("#formCard").classList.add("d-none")
    // 9. display each result to card
    document.getElementById("result").innerHTML =
        `
    <p>English Name: ${enName}</p>
    <p>Khmer Name: ${khName}</p>
    <p>Gender: ${gender.value}</p>
    <p>Email: ${email}</p>
    <p>Phone: ${phone}</p>
    <p>Subject: ${subjectSelect}</p>
    `
}
// 10 reset form (clear message and clear value in each input)
function resetForm() {
    event.preventDefault();
    document.querySelector("#studentForm").reset();
    errorTag.forEach(err => {
        err.innerText = ""

    });
}
````

# Classwork 1 - HTML Code

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Student Registration</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
</head>

<body>

    <div class="container mt-5">

        <!-- FORM CARD -->
        <div class="row justify-content-center" id="formCard">
            <div class="col-md-8">
                <div class="card shadow">
                    <div class="card-header bg-primary text-white text-center">
                        <h4>Student Registration</h4>
                    </div>

                    <div class="card-body">
                        <form id="studentForm">

                            <!-- English Name -->
                            <div class="mb-3">
                                <label class="form-label">English Name</label>
                                <input type="text" id="enName" class="form-control">
                                <small class="text-danger" id="enNameError"></small>
                            </div>

                            <!-- Khmer Name -->
                            <div class="mb-3">
                                <label class="form-label">Khmer Name</label>
                                <input type="text" id="khName" class="form-control">
                                <small class="text-danger" id="khNameError"></small>
                            </div>

                            <!-- Gender -->
                            <div class="mb-3">
                                <label class="form-label">Gender</label><br>
                                <input type="radio" name="gender" value="Male"> Male
                                <input type="radio" name="gender" value="Female"> Female <br>
                                <small class="text-danger" id="genderError"></small>
                            </div>

                            <!-- Email -->
                            <div class="mb-3">
                                <label class="form-label">Email</label>
                                <input type="email" id="email" class="form-control">
                                <small class="text-danger" id="emailError"></small>
                            </div>

                            <!-- Phone -->
                            <div class="mb-3">
                                <label class="form-label">Phone</label>
                                <input type="text" id="phone" class="form-control">
                                <small class="text-danger" id="phoneError"></small>
                            </div>

                            <!-- Subjects -->
                            <div class="mb-3">
                                <label class="form-label">Subjects</label><br>
                                <input type="checkbox" class="subject" value="JavaScript"> JavaScript <br>
                                <input type="checkbox" class="subject" value="PHP"> PHP <br>
                                <input type="checkbox" class="subject" value="Java"> Java <br>
                                <input type="checkbox" class="subject" value="C#"> C# <br>
                                <small class="text-danger" id="subjectError"></small>
                            </div>

                            <div class="text-center">
                                <button type="submit" class="btn btn-success" onclick="addStudent()">Submit</button>
                                <button type="submit" class="btn btn-primary" onclick="resetForm()">Reset</button>
                            </div>

                        </form>
                    </div>
                </div>
            </div>
        </div>

        <!-- RESULT CARD -->
        <div class="row justify-content-center d-none" id="resultCard">
            <div class="col-md-8">
                <div class="card shadow">
                    <div class="card-header bg-success text-white text-center">
                        <h4>Student Information</h4>
                    </div>
                    <div class="card-body" id="result">

                    </div>
                </div>
            </div>
        </div>

    </div>

    <script src="classwork1.js">

    </script>

</body>

</html>
```
