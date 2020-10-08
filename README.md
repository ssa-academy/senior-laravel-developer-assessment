<h1 align="center">
Assessment for Senior Laravel Developer
</h1>

<br>

This assessment is designed to test an examinee’s knowledge of PHP Design principles and its implementations on Laravel framework. It is divided into 3 levels, with each feature having to be accomplished consecutively.

**Assessment Point System**: The assessment total is 120 points with additional 32 bonus points.
Passing grade is 100. See breakdown below for more detail.

| Levels  | Points | Bonus | Total |
| ------- | -----: | ----: | ----: |
| Level 1 |     45 |    12 |    57 |
| Level 2 |     45 |    10 |    55 |
| Level 3 |     30 |    10 |    40 |
|   TOTAL |    120 |    32 |   152 |

**Asessment Duration**: Examinee is given 3 days to complete the assessment. On a separate spreadsheet, please log the time spent per functionality (start time and end time).
For any questions regarding the exam please send inquiry to <a href="mailto:john@ssagroup.com">john@ssagroup.com</a> or HR.

**Output**: Examinee is expected to send an email with attachment to their output files (preferably a zip file). Alternatively, examinee can attach a link to their GitHub Repository, Google Drive, Dropbox, or any other storage service to download the output if the file is too large to be attached on email, or for other reason.

<br>
<hr>
<br>

<a name="Level-1"></a>
### Level 1

##### Goals

- [ ] Implement Laravel’s default login feature
- [ ] Develop User CRUD functionalities

<br>

##### Prerequisites

Base the user migration file in the following table:

```mysql
mysql> show columns from users;
+-------------------+-----------------+------+-----+---------+----------------+
| Field             | Type            | Null | Key | Default | Extra          |
+-------------------+-----------------+------+-----+---------+----------------+
| id                | bigint unsigned | NO   | PRI | NULL    | auto_increment |
| prefixname        | varchar(255)    | YES  |     | NULL    |                |
| firstname         | varchar(255)    | NO   |     | NULL    |                |
| middlename        | varchar(255)    | YES  |     | NULL    |                |
| lastname          | varchar(255)    | NO   |     | NULL    |                |
| suffixname        | varchar(255)    | YES  |     | NULL    |                |
| username          | varchar(255)    | NO   | UNI | NULL    |                |
| email             | varchar(255)    | NO   | UNI | NULL    |                |
| password          | text            | NO   |     | NULL    |                |
| photo             | text            | YES  |     | NULL    |                |
| type              | varchar(255)    | YES  | MUL | user    |                |
| remember_token    | varchar(100)    | YES  |     | NULL    |                |
| email_verified_at | timestamp       | YES  |     | NULL    |                |
| created_at        | timestamp       | YES  |     | NULL    |                |
| updated_at        | timestamp       | YES  |     | NULL    |                |
| deleted_at        | timestamp       | YES  |     | NULL    |                |
+-------------------+-----------------+------+-----+---------+----------------+
```

<br>

##### Instructions

1. Start a project in Laravel 7 or higher
1. Implement the default login feature using the [laravel/ui](https://packagist.org/packages/laravel/ui) package.
1. Add a page to list all users (users.index) in a table.
1. Add a page to display the form to create a new user (users.create).
1. Add a page to edit a user (users.edit / users.update).
1. Add a button to delete a user (users.destroy).
1. Add a page to list all soft deleted users (users.trashed).
1. Add a button to restore a soft deleted user (users.restore).
1. Add a button to permanently delete a soft deleted user (users.delete).

<br>

##### ✎ Notes
- All users routes must implement the `auth` middleware.
- The value for `prefixname` should only be one in `[Mr, Mrs, Ms]`.
- Use `enctype=multipart/form-data` attribute on the `forms` to enable uploading of user photo.

<br>

##### ★ Bonus

- **+5 points** - Write and register a route macro for soft deletes, which can be used as:
	```php
	Route::softDeletes('users', 'UserController');
	```

	which contains the following route collection:
	```php
	Route::get('users/trashed', 'UserController@trashed')->name('users.trashed');
	Route::patch('users/{user}/restore', 'UserController@restore')->name('users.restore');
	Route::delete('users/{user}/delete', 'UserController@delete')->name('users.delete');
	```

- **+2 points** - Implement a model accessor called `getAvatarAttribute` which can be used as:
	```php
	$user = User::find(1);
	$user->avatar;
	```
	
	<details>
	<summary>which returns the photo or a default image</summary>
	
	```php
	/**
	 * Retrieve the default photo from storage.
	 * Supply a base64 png image if the `photo` column is null.
	 *
	 * @return string
	 */
	public function getAvatarAttribute(): string
	{
		// Code goes brrrr.
	}
	```
	</details>

- **+2 points** - Implement a model accessor called `getFullnameAttribute` which can be used as:
	```php
	$user = User::find(1);
	$user->fullname; // E.g. Juan P. dela Cruz
	```
	<details>
	<summary>which returns the first, middle initial, and last name of the user</summary>
	
	```php
	/**
	 * Retrieve the user's full name in the format:
	 *  [firstname][ mi?][ lastname]
	 * Where:
	 *  [ mi?] is the optional middle initial.
	 *
	 * @return string
	 */
	public function getFullnameAttribute(): string
	{
		// Code goes brrrrr.
	}
	```
	</details>

- **+2 points** - Implement a model accessor called `getMiddleinitialAttribute` which can be used as:
	```php
	$user = User::find(1);
	$user->middleinitial; // E.g. "delos Santos" -> "D."
	```

- **+1 point** - Style the pages using a preferred framework (e.g. bootstrap, vuetify, etc.).

<br>
<hr>
<br>

<a name="Level-2"></a>
### Level 2

##### Goals

- [ ] Implement a Service Pattern for User CRUD
- [ ] Write Unit testing for the service class
- [ ] Add Validation rules to the User CRUD

<br>

##### Prerequisites

- [Level 1](#Level-1) must be fully accomplished
- [phpunit/phpunit](https://packagist.org/packages/phpunit/phpunit) package must be installed

<br>

##### Instructions

1. Create a unit test file in `/tests/Unit/Services/UserServiceTest.php`
	<details>
 	<summary>Sample UserServiceTest.php</summary>
	
	```php

	namespace Tests\Unit\Services;

	use Illuminate\Foundation\Testing\DatabaseMigrations;
	use Illuminate\Foundation\Testing\RefreshDatabase;
	use Illuminate\Foundation\Testing\WithFaker;
	use Tests\TestCase;

	/**
	 * @runTestsInSeparateProcesses
	 * @preserveGlobalState disabled
	 */
	class UserServiceTest extends TestCase
	{
	    use DatabaseMigrations, RefreshDatabase, WithFaker;

	    /**
	     * @test
	     * @return void
	     */
	    public function it_can_return_a_paginated_list_of_users()
	    {
		// Arrangements

		// Actions

		// Assertions
	    }

	    /**
	     * @test
	     * @return void
	     */
	    public function it_can_store_a_user_to_database()
	    {
		// Arrangements

		// Actions

		// Assertions
	    }

	    /**
	     * @test
	     * @return void
	     */
	    public function it_can_find_and_return_an_existing_user()
	    {
		// Arrangements

		// Actions

		// Assertions
	    }

	    /**
	     * @test
	     * @return void
	     */
	    public function it_can_update_an_existing_user()
	    {
		// Arrangements

		// Actions

		// Assertions
	    }

	    /**
	     * @test
	     * @return void
	     */
	    public function it_can_soft_delete_an_existing_user()
	    {
		// Arrangements

		// Actions

		// Assertions
	    }

	    /**
	     * @test
	     * @return void
	     */
	    public function it_can_return_a_paginated_list_of_trashed_users()
	    {
		// Arrangements

		// Actions

		// Assertions
	    }

	    /**
	     * @test
	     * @return void
	     */
	    public function it_can_restore_a_soft_deleted_user()
	    {
		// Arrangements

		// Actions

		// Assertions
	    }

	    /**
	     * @test
	     * @return void
	     */
	    public function it_can_permanently_delete_a_soft_deleted_user()
	    {
		// Arrangements

		// Actions

		// Assertions
	    }

	    /**
	     * @test
	     * @return void
	     */
	    public function it_can_upload_photo()
	    {
		// Arrangements

		// Actions

		// Assertions
	    }
	}

	```
	</details>
1. Create a file `/app/Services/UserService.php`
	<details>
	<summary>Sample UserService.php</summary>
	
	```php

        namespace User\Services;

        use App\User;
        use Illuminate\Http\Request;
        use Illuminate\Http\UploadedFile;
        use Illuminate\Pagination\LengthAwarePaginator;

        class UserService implements UserServiceInterface
        {
            /**
             * The model instance.
             *
             * @var App\User
             */
            protected $model;

            /**
             * The request instance.
             *
             * @var \Illuminate\Http\Request
             */
            protected $request;

            /**
             * Constructor to bind model to a repository.
             *
             * @param \App\User                $model
             * @param \Illuminate\Http\Request $request
             */
            public function __construct(User $model, Request $request)
            {
                $this->model = $model;
                $this->request = $request;
            }

            /**
             * Define the validation rules for the model.
             *
             * @return array
             */
            public function rules()
            {
                return [
                    /**
                     * Rule syntax:
                     *  'column' => 'validation1|validation2'
                     *
                     *  or
                     *
                     *  'column' => ['validation1', function1()]
                     */
                    'firstname' => 'required',
                ];
            }

            /**
             * Retrieve all resources and paginate.
             *
             * @return \Illuminate\Pagination\LengthAwarePaginator
             */
            public function list()
            {
                // Code goes brrrr.
            }

            /**
             * Create model resource.
             *
             * @param  array $attributes
             * @return \Illuminate\Database\Eloquent\Model
             */
            public function store(array $attributes)
            {
                // Code goes brrrr.
            }

            /**
             * Retrieve model resource details.
             * Abort to 404 if not found.
             *
             * @param  integer $id
             * @return \Illuminate\Database\Eloquent\Model|null
             */
            public function find(int $id):? Model
            {
                // Code goes brrrr.
            }

            /**
             * Update model resource.
             *
             * @param  integer $id
             * @param  array   $attributes
             * @return boolean
             */
            public function update(int $id, array $attributes): bool
            {
                // Code goes brrrr.
            }

            /**
             * Soft delete model resource.
             *
             * @param  integer|array $id
             * @return void
             */
            public function destroy($id)
            {
                // Code goes brrrr.
            }

            /**
             * Include only soft deleted records in the results.
             *
             * @return \Illuminate\Pagination\LengthAwarePaginator
             */
            public function listTrashed()
            {
                // Code goes brrrr.
            }

            /**
             * Restore model resource.
             *
             * @param  integer|array $id
             * @return void
             */
            public function restore($id)
            {
                // Code goes brrrr.
            }

            /**
             * Permanently delete model resource.
             *
             * @param  integer|array $id
             * @return void
             */
            public function delete($id)
            {
                // Code goes brrrr.
            }

            /**
             * Generate random hash key.
             *
             * @param  string $key
             * @return string
             */
            public function hash(string $key): string
            {
                // Code goes brrrr.
            }

            /**
             * Upload the given file.
             *
             * @param  \Illuminate\Http\UploadedFile $file
             * @return string|null
             */
            public function upload(UploadedFile $file)
            {
                // Code goes brrrr.
            }
        }

	```
	</details>
1. Create an Interface class file `/app/Services/UserServiceInterface.php`
	1. The Interface must have a public method called `hash(string $password): string`, which should be used when saving the password field.
1. Build your test cases. See the following test cases for the minimum coverage requirements:
	```
	+-------------------------------------------------+---------------------------------------------------------+------------------------------------------------------------------------------------+
	| Unit Test                                       | Coverage                                                | Remarks                                                                            |
	+-------------------------------------------------+---------------------------------------------------------+------------------------------------------------------------------------------------+
	| it_can_return_a_paginated_list_of_users         | UserService@list                                        | Must implement and return the \Illuminate\Pagination\LengthAwarePaginator instance |
	| it_can_store_a_user_to_database                 | UserService@store(array $attributes)                    |                                                                                    |
	| it_can_find_and_return_an_existing_user         | UserService@find(int $id)                               | Must use the findOrFail method of the User model.                                  |
	| it_can_update_an_existing_user                  | UserService@update(int $id, array $attributes)          |                                                                                    |
	| it_can_soft_delete_an_existing_user             | UserService@destroy(int $id)                            |                                                                                    |
	| it_can_return_a_paginated_list_of_trashed_users | UserService@listTrashed                                 |                                                                                    |
	| it_can_restore_a_soft_deleted_user              | UserService@restore(int $id)                            |                                                                                    |
	| it_can_permanently_delete_a_soft_deleted_user   | UserService@delete(int $id)                             |                                                                                    |
	| it_can_upload_photo                             | UserService@upload(\Illuminate\Http\UploadedFile $file) |                                                                                    |
	+-------------------------------------------------+---------------------------------------------------------+------------------------------------------------------------------------------------+
	```
1. If all test passed, inject the `UserService` instance to the `UserController@__construct` method.
1. Use the `UserService`'s methods inside `UserController` accordingly.
1. Add validation rules to the `UserService@rules`.
1. Create a `Request` class file `/app/Http/Requests/UserRequest.php`, and add the rules.
	<details>
	<summary>See sample `UserRequest.php`</summary>
	
	```php

	namespace App\Http\Requests;

	use Illuminate\Foundation\Http\FormRequest;
	use App\Services\UserServiceInterface;

	class UserRequest extends FormRequest
	{
	    /**
	     * Determine if the user is authorized
	     * to make this request.
	     *
	     * @return boolean
	     */
	    public function authorize()
	    {
		return true;
	    }

	    /**
	     * Get the validation rules that apply to the request.
	     *
	     * @return array
	     */
	    public function rules()
	    {
		return $this->container->make(
		    UserServiceInterface::class
		)->rules();
	    }
	}


	```
	</details>
1. Use the `UserRequest` class as the first parameter to `UserController@store` and `UserController@update`.
	```php
	public function store(UserRequest $request) { ... }

	public function update(UserRequest $request, int $id) { ... }
	```

<br>

##### ✎ Notes

- You may add more test cases to cover more methods if desired.
- If a method name is not to your liking, you may change it thus.
- Use your best judgment in declaring the validation rules for the fields.

<br>

##### ★ Bonus

- **+5 points** - Write a **Feature test** covering the `UserController` functionalities
- **+5 points** - Write a **Unit test** covering the `<timestamp>_create_users_table` migration columns.


<br>
<hr>
<br>

<a name="Level-3"></a>
### Level 3

##### Goals

- [ ] Generate a table called <code>details</code> to save additional user background information
- [ ] Implement an event called <code>UserSaved</code> triggered when a user is created or updated
- [ ] Implement a listener that auto-saves additional user details when the <code>UserSaved</code> event is triggered

<br>

##### Prerequisites

- [Level 2](#Level-2) must be fully accomplished

<br>

##### Instructions

1. Create a migration file for a table called `details`. Use the following table for reference on the columns:
	```mysql
	mysql> show columns from details;
	+------------+-----------------+------+-----+---------+----------------+
	| Field      | Type            | Null | Key | Default | Extra          |
	+------------+-----------------+------+-----+---------+----------------+
	| id         | bigint unsigned | NO   | PRI | NULL    | auto_increment |
	| key        | varchar(255)    | NO   | MUL | NULL    |                |
	| value      | text            | YES  |     | NULL    |                |
	| icon       | varchar(255)    | YES  |     | NULL    |                |
	| status     | varchar(255)    | NO   |     | 1       |                |
	| type       | varchar(255)    | YES  |     | detail  |                |
	| user_id    | bigint unsigned | YES  | MUL | NULL    |                |
	| created_at | timestamp       | YES  |     | NULL    |                |
	| updated_at | timestamp       | YES  |     | NULL    |                |
	+------------+-----------------+------+-----+---------+----------------+
	```
1. Generate an Eloquent Model file `app/Detail.php`.
1. Assign a one-to-many relationship between `App\User` model and `App\Detail` model.
1. Create an Event class `app/Events/UserSaved`.
1. Map the `UserSaved` Event class on `App\User` Eloquent Model's `saved` event.
1. Create a Listener class `app/Listeners/SaveUserBackgroundInformation.php`.
	1. Inject the `UserService` class on `SaveUserBackgroundInformation@__construct`.
		1. Add a method in the `UserService` class to handle saving of user details.
	1. The listener should save to a table called `details` the following information:
		1. The user's full name based on `firstname`, `middlename`, and `lastname`.
		1. The user's middle initial based on abbreviating the `middlename`.
		1. The user's avatar based on a given `photo`
		1. The user's gender based on the value of `prefixname`.
		<details>
		<summary>Example data:</summary>
		
		```php
		for a user with attributes of:
		{
			id: 1,
			prefixname: 'Mr.',
			firstname: 'Juan',
			middlename: 'Palito',
			lastname: 'dela Cruz',
			suffixname: 'Jr.',
			username: 'juantwothree',
			email: 'juan@demo.ph',
			photo: null,
			type: 'user',
		}
		```
		
		The saved data should be:

		```mysql
		mysql> select `id`,`key`,`value`,`type`,`user_id` from details;
		+----+-----------------+-------------------+--------+---------+
		| id | key             | value             | type   | user_id |
		+----+-----------------+-------------------+--------+---------+
		|  1 | Full name       | Juan P. dela Cruz | bio    |       1 |
		|  2 | Middle Initial  | P.                | bio    |       1 |
		|  3 | Avatar          | http://localho... | bio    |       1 |
		|  4 | Gender          | Male              | bio    |       1 |
		+----+-----------------+-------------------+--------+---------+
		```
		</details>

<br>

##### ✎ Notes

- The column `details.user_id` must be a foreign key that references `users.id` and cascades on `DELETE` and `UPDATE`

<br>

##### ★ Bonus

- **+5 points** - Write a **Unit test** covering the event listener's functionalities
- **+5 points** - Write a **Feature test** covering the event and event listener's functionalities


<br>
<hr>
<br>

<p align="center">
:four_leaf_clover:  Good luck!
</p>

<p align="center">
<small>Prepared for SSA Academy Software and Web Developer Applicants only.<br>Do not reproduce document elsewhere.</small>
</p>
