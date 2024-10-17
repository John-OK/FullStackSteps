# FullStackSteps
Steps to set up a full stack application with Django in the backend and React/Vite in the frontend

# The Steps
0. (OPTIONAL but highly recommended)Go to GitHub and start a new repository. When creating it, be sure to add .gitignore file using the python template (for a Django project), a license (MIT if unsure), and at least a short description of the project. Then on your local system go to the directory where you want your project folder to be in and run `git clone <get the URL from your github repo page>`
1. Create a folder to contain entire project (i.e., front and back ends); cd to folder (If you cloned your project, just cd into the folder.)
2. Create virtual environment and "source" it:
```
python -m venv .venv
source .venv/bin/activate
```
3. Install Django, psycopg (PostgreSQL adapter for Python) (use psycopg v2 if using Python < v3.8), and, Django React Framework (optional here, may cause errors if installed too soon):
```
pip install django psycopg djangorestframework
```
4. Make requirements.txt of installed packages:
```
pip freeze > requirements.txt
```
5. Create Django project:
```
python -m django startproject <name of django project folder> #e.g., "todo_list_project"
```
Now, there will be a folder with the name of the project in the current directory as well as a subfolder in the newly created project folder with the same name. Rename the upper most project folder if desired.

6. (OPTIONAL) Rename folder with project name to "backend" (or something like that, e.g., "todo_list_backend"):\
`mv <project name> backend`
7. Make directory "static" in "backend" folder (this is where the React/Vite will put files for the Django to access):
```
cd backend
mkdir static
```
8. Create Django app (should still be in backend folder):
```
python manage.py startapp <app name> # e.g., "todo_list_app"
```
9. Configure 'settings.py' (located in project folder created in step 5):\
    a. Register app in INSTALLED_APPS by adding name of app folder from step 8. It should look similar to this:
   ```
   INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'todo_list_app',
   ]
   ```
    b. Set static folder location. After "STATIC_URL = 'static/'", add:
    ```
    STATICFILES_DIRS = [BASE_DIR / 'static']
    ```
10. In 'urls.py' (in project folder),:\
     a. import "include"; line should now look like:
     ```
     from django.urls import path, include
     ```
     b. route to app in urlpatterns:
     ```
     path('', include('todo_list_app.urls')),
     ```
11. Create "urls.py" file in *app* folder and add route to homepage html file, e.g.,:
```
from django.urls import path
from . import views

urlpatterns = [
    path('', views.homepage),
]
```
12. Create view in views.py to display, "hello world", in homepage, e.g.,:
```
from django.shortcuts import render
from django.http import HttpResponse

def homepage(request):
    print('LOADING HOMEPAGE')
    return HttpResponse("HELLO WORLD!")
```
(START DJANGO FOLDER AND CHECK THAT PAGE LOADS):
```
python manage.py runserver
```
14. cd to "fullstack" folder (i.e., projects root folder and the folder where .venv foder is located)
15. Create Vite project and name the project whatever the folder should be named, e.g., "frontend". Choose "react" in the selection options that follow (or "react-ts" is using typescript)
```
npm create vite
```

16. CD into newly created folder (e.g., "frontend")<br/>

17. Install Vite dependencies:
```
npm install
```
18. NOTE: This step may not be needed. First check if typescript is installed locally as a dev dependency:
`npx tsc -v`
If there is no error, and a version number is returned, there is no need to install typescript, and you can skip to the next step. Otherwise, if an error is returned, continue with this step.
If using typescript, install typescript:<br/>
`npm install typescript --save-dev`

19. Configure "vite.config.js" (or "vite.config.ts" if using typescritp) to link with Django. Ensure 'outDir' points to Django's static folder. This configuration will allow Django to see the frontend files (be sure the "outDir" points to your static file; "backend" should be replaced with the directory name of your Django project directory):
```
export default defineConfig({

  // vite uses this as a prefix for href and src URLs
  base: '/static/',
  build: {
    // this is the folder where vite will generate its output. Make sure django can serve files from here!
    outDir: '../backend/static',
    emptyOutDir: true,
    sourcemap: true, // aids in debugging by giving line numbers and readable code
  },
  plugins: [react()]
})
```
20. Run the Vite build (NOTE: we use `npm run build` here instead of `npm run dev` because the development environment won't output to Django's 'static' folder):
```
npm run build
```
21. CD to django project folder where 'manage.py' is
2. Check if everything is setup correctly:
```
python manage.py runserver
```
23. Setup Vite's `package.json` file to automatically update changes in Vite's src folder to Django's static folder by adding the following to the scripts section of `package.json` (don't forget to add a comma if necessary):
```
"watch": "vite build --watch"
```
24. Create a new terminal (optional) and cd to folder where package.json resides
25. Start the watch script:
```
npm run watch
```
26. In models.py, prepare AppUser model:
```
from django.contrib.auth.models import AbstractUser

class AppUser(AbstractUser):
    email = models.EmailField(
        verbose_name='email address',
        max_length=255,
        unique=True,
    )


    USERNAME_FIELD = 'email'
    REQUIRED_FIELDS = [] # Email & Password are required by default.
```
27. Register AppUser in settings.py (put between 'DATABASES' AND 'AUTH_PASSWORD_VALIDATORS'):
```
AUTH_USER_MODEL = '<app foldername created is step 8>.AppUser'
```
27. Set ENGINE to postgresql and NAME to DB name in configure database in settings.py (if not using sqlite, be sure NAME field does not have 'BASE_DIR /' before DB name, otherwise "'PosixPath' has no len()" error is thrown):
NOTE: To create a database from the Linux CLI: `createdb <database_name>`
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': '<DB name>',
    }
}
```
29. Import modules for user authentication to views.py:
```
from django.contrib.auth import authenticate, login, logout
```
30. If Django Rest Framework wasn't installed in step 3 (update requirement.txt if installing here):
```
pip install djangorestframework
```
31. Import api_view in views.py (api_view is a decorator that tells Django that the view is for the API/AJAX, not regular browser request, and will send/receive JSON data. It can be passed a list as an argument specifying which requests methods the view is allowed to handle, e.g., POST. It also parses data from requests into JSON format accessible with 'request.data'. Since it specifies what request methods are valid, there is no need for if statements to handle different methods):
```
from rest_framework.decorators import api_view
```
32. Add views (with api_view decorator) for signup, login, logout, whoami
33. Import serializers in view.py (converts backend data into JSON to send to frontend):
```
from django.core import serializers

# Example of how to use:
data = serializers.serialize("json", [request.user], fields=['email', 'username'])
# json - serialize as JSON format
# request.user - data to be serialize
# fields = [] - (optional) fields from table to include in output 
```
34. Add url patterns (paths) to urls.py
35. Make migrations and migrate
36. Add signup, login, logout functionality in React (should be separate components or files). Login form should hard reload in order to get CSRF token from Django:
```
window.location.reload()
```
37. Install axios from frontend folder:
```
npm install axios
```
38. Import axios into App.jsx:
```
import axios from 'axios'
```
39. Quit (CTRL-C) and restart watcher:
```
npm run watch
```
40. Add getCSRFToken function and set default axios header to above App component in App.jsx:
    copy code from Django docs:
        https://docs.djangoproject.com/en/4.0/ref/csrf/#acquiring-csrf-token-from-cookie
```
function getCookie(name) {
    let cookieValue = null;
    if (document.cookie && document.cookie !== '') {
        const cookies = document.cookie.split(';');
        for (let i = 0; i < cookies.length; i++) {
            const cookie = cookies[i].trim();
            // Does this cookie string begin with the name we want?
            if (cookie.substring(0, name.length + 1) === (name + '=')) {
                cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                break;
            }
        }
    }
    return cookieValue;
}
const csrftoken = getCookie('csrftoken');
axios.defaults.headers.common['X-CSRFToken'] = csrftoken
```

# React JuJu

## React Router

1. Install React Router Module (note: '--save' only needed for pre npm5):
```
npm install react-router-dom --save
```
2. In App.js, import Router components:
```
import { HashRouter as Router, Routes, Route } from 'react-router-dom';
```
3. Enclose App() return section in Router component:
```
function App() {
    return (
        <Router>
            <div>
                ...
            </div>
        </Router>
    )
    }
}
```
4. Add Routes component and have all routes within it:
```
function App() {
    return (
        <Router>
            <div>
                <Routes>
                    <Route>
                        ...
                    </Route>
                </Routes>
            </div>
        </Router>
    )
    }
}
```
5. Include the path in each Route component:
```
function App() {
    return (
        <Router>
            <div>
                <Routes>
                    <Route path="/">
                        <Homepage />
                    </Route>
                </Routes>
            </div>
        </Router>
    )
    }
}
```

# React Bootstrap

1. Import React-Bootstrap and Bootstrap
```
npm install react-bootstrap bootstrap
```
2. Get component examples from [React-Bootstrap](https://react-bootstrap.github.io/components/alerts)


# NOTES
- API call to Abstract was getting called twice because whoAmI (useEffect) was getting called on page load then again after page loaded. Since I had the API call on page load, it was also getting called twice.
- API call was getting called whenever entering a char in input field because it useState was setting state of input field on each char input, and refreshing the page, thus making the API call. This seems like it should not be happening since React is supposed to only render the part that needs updating. Need to research further. Can check out commit from Aug 9 with comment "Moved BirdMap component call to homepage.jsx" (hash= 9d69b28...) to see issue. Next commit on Aug 9 is with multi-calls fixed.
