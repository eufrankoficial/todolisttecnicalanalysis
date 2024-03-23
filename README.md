
# Todo List Application

```markdown
   Stack
  - Inertiajs: VueJs + Laravel
  - Sail/docker
  - Mysql
  - phpunit (integration tests)
  - cypress (integration test)
```

For a smaller project like this, I suggest using both the backend and frontend in the same application.
I suggest using inertiajs which already brings an abstraction with frontend and backend in the same application, allowing you to choose
to use react or vuejs on the frontend and Laravel on the backend.

In addition to being a great option for solving the SPA problem, it is easy to implement
and we can run through a Laravel sail, the front with npm or even run with php artisan serve.

Regarding backend architecture, considering a possible growth of the application in the future,
I see the concept of clean architecture as a viable option in terms of architectural organization and strong incentive
to decouple business rules from other layers of the application.

Created by Robert C. Martin, Clean Architecture is a software architecture pattern that aims to
separate software concerns, dividing software into layers.

This proposal can provide code reusability in other parts of our software, such as applying
creating activities through artisan commands, deleting a specific activity, and/or even changing
the status of an activity using existing code that is decoupled and independent, without the need for
creating new codes to handle business logic.

Let's adapt within our Laravel project.

First, let's create a new application:

```
composer create-project laravel/laravel:^11.0 example-app
```

Second, we can install our server-side and client-side of inertia js

https://inertiajs.com/server-side-setup
https://inertiajs.com/client-side-setup

Architecture

Within our app folder, readjust our models directory to within a directory called Domain, where we will also have the interfaces of our Repositories, Services, and UseCases.

In order to clarify the structure related to the core domain, we can define it as follows:

```
App
  - Domain
    - Models
      - Category
      - Priority
      - Activity
      - ActivityStatus
    - Repositories
      - CategoryRepositoryInterface
      - PriorityRepositoryInterface
      - ActivityRepositoryInterfae
    - Services
      - ExternalCallServiceInterface
    - UseCases
      - CategoryUseCaseInterface
      - PriorityUseCaseInterface
      - ActivityUseCaseInterface
```

Something important is that in our domain we have clear and really important rules for our business, such as checking
deadlines, checking priorities, and etc.

With our core domain defined, let's move on to the next layer, the layer that will implement our repositories and services. This layer will enable communication with our business rules (domain).
In services we can make external calls if we deem it necessary.

```
App
  - Infra
    - Repositories
      - CategoryRepository<CategoryRepositoryInterface>
      - PriorityRepository<CategoryRepositoryInterface>
      - ActivityRepository<ActivityRepositoryInterfae>
    - Services
      - ExternalCallService<ExternalCallServiceInterface>
```

I also think it's interesting to create a DTO (Data transfer object), in which we will translate our objects to
the innermost part of our architecture.

```
App
  - DTO
    - CategoryDTO
    - PriorityDTO
    - ActivityDTO
```

Then I found it interesting to create a use case layer where we will implement the respective interfaces related
to our business rules (domain). The interesting thing about this part here is that, by doing the implementation for example of business rules independently,
it becomes possible to reuse and call this layer in places like artisan commands and to execute a specific rule
from cron jobs. If we need for example a use case for deadline, priority, calculation of activity completion time, or something like that, we can create this interface and model in our domain and implement it here.

```
App
  - UseCases
    - CategoryUseCase<CategoryUseCaseInterface>
    - PriorityUseCase<PriorityUseCaseInterface>
    - ActivityUseCase<ActivityUseCaseInterface>
```

After that, we will have the most external part of our application, which are our controllers.
This is the part where we will have less code because it is responsible only for calling other parts of the application (use cases).
In addition to calling use cases, the controller will also assume the role of returning and calling our components on the frontend.

```
App
  - Http
    - Controllers
      - CategoryController
      - PriorityController
      - ActivityController
    - Middlewares
    - Requests
      - CategoryStoreRequest
      - CategoryUpdateRequest
      - PriorityStoreRequest
      - PriorityUpdateRequest
      - ActivityStoreRequest
      - ActivityUpdateRequest
```

If necessary, we can test our endpoints with phpunit. Inertiajs has an abstraction in which in addition to testing our calls (update, create, delete), it also allows us to
test if frontend components are being called correctly and have the correct properties.

FRONTEND

As mentioned at the beginning of our analysis, we will use inertiajs with vuejs to build our frontend.
Note: I'm not going to address authentication situations, as this is not part of the test suggestion. But Laravel already has several ways to handle authentication and acl through policies and authorizations, I believe understanding the requirements of this part is important to implement a viable solution.

I chose to use inertiajs because it is a solution that integrates very easily with Laravel, in addition to not needing to completely
from scratch the frontend integration via api token and etc. In the future, if necessary, it is also very easy to switch the frontend to react
or even turn the project into an api and build the frontend separately.

Inertiajs already has state management (useRemember) - which for us is ideal - to save our forms, edit our activities, list and delete.
Inertiajs also allows integration with Vue2, React, and Svelte.

In addition, Inertia's own documentation encourages the use of cypress or laravel dusk for end-to-end testing.

For unit testing, if necessary, we can use jest or motchajs.

For more information, we can consult the documentation

https://inertiajs.com

The structure of our frontend can be built in a simpler and more intuitive way, but it meets our needs well.

within our laravel resources folder:

```
- resources
  - js 
    - Pages
    - components
    - Layouts
```

Final Considerations
- According to the proposed requirements, backend and frontend are well served with architectural solutions. Of course, with each new problem we seek to solve with a best practice or concept we add a layer of complexity
to our code. Both parts of the system are important and I believe that with a layered architecture in the backend and a good frontend "framework" proposed with inertiajs
we can develop the project efficiently and quickly.
