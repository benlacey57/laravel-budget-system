### Users Schema
The `users` table stores information about the users of the application. Each user can have multiple budgets and bank accounts associated with them.

#### Schema Considerations
- The `email` field is unique to ensure that each user can only register once with a specific email address.
- The `password` field should be securely hashed before storage (Laravel does this automatically if you use its Auth system).
- `rememberToken` is used for the "Remember Me" functionality during login.

```php
Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('email')->unique();
    $table->timestamp('email_verified_at')->nullable();
    $table->string('password');
    $table->rememberToken();
    $table->timestamps();
});
```

### Budgets Schema
The `budgets` table holds information about budgets created by users. Each budget is linked to a specific user and can have multiple transactions.

#### Schema Considerations
- The `user_id` field is a foreign key that links each budget to its owner in the `users` table.
- `total_amount` represents the total amount allocated for this budget.

```php
Schema::create('budgets', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
    $table->string('name');
    $table->decimal('total_amount', 10, 2);
    $table->timestamps();
});
```

### Loans and Debts Schema
The loans_debts table stores information about the user's loans and debts, including a description, start and end dates, and the monthly payment amount.

#### Schema Considerations
- `start_date and end_date allow tracking the duration of each loan or debt.
- Consider implementing soft deletes to retain records of cleared debts for historical analysis.

```php
Schema::create('loans_debts', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
    $table->string('name');
    $table->text('description')->nullable();
    $table->date('start_date');
    $table->date('end_date')->nullable();
    $table->decimal('monthly_payment', 10, 2);
    $table->enum('type', ['loan', 'debt']);;
    $table->timestamps();
});
```

### Pensions Schema
The pensions table is dedicated to tracking pension contributions and the current balance of the user's pension accounts.

#### Schema Considerations
- The monthly_contribution field is nullable to accommodate pensions that may not have regular contributions.
- Tracking the current_balance helps users monitor the growth of their pension savings over time.

```php
Schema::create('pensions', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
    $table->string('name');
    $table->decimal('monthly_contribution', 10, 2)->nullable();
    $table->decimal('current_balance', 10, 2);
    $table->timestamps();
});
```

### Savings Table
The savings table records the user's savings accounts, including monthly contributions and the current account balance.

#### Schema Considerations
- `monthly_contribution is nullable to account for varying saving habits.
- The focus on current_balance provides a clear picture of the user's saving progress.

```php
Schema::create('savings', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
    $table->string('name');
    $table->decimal('monthly_contribution', 10, 2)->nullable();
    $table->decimal('current_balance', 10, 2);
    $table->timestamps();
});
```

### Transactions Schema
The `transactions` table stores individual transactions. Each transaction is linked to a budget and optionally to a category.

#### Schema Considerations
- `budget_id` links the transaction to a budget.
- `category_id` is optional and links the transaction to a specific category, allowing for more detailed financial tracking and reporting.
- `amount` and `transaction_date` record the value and date of the transaction.

```php
### Transactions Schema

```php
Schema::create('transactions', function (Blueprint $table) {
    $table->id();
    $table->foreignId('budget_id')->constrained()->onDelete('cascade');
    $table->date('transaction_date');
    $table->string('description');
    $table->enum('type', ['income', 'expense', 'loan', 'debt']);
    $table->foreignId('loan_debt_id')->nullable()->constrained('loans_debts')->onDelete('set
    $table->decimal('money_in', 10, 2)->nullable();
    $table->decimal('money_out', 10, 2)->nullable();
    $table->decimal('balance', 10, 2);
    $table->timestamps();
});


```

### Categories Schema (Optional)

#### Description
The `categories` table is an optional schema that can be used to categorize transactions for more granular financial tracking.

#### Schema Considerations
- Categories are defined with a simple `name` field.
- Each category can be associated with multiple transactions.

```php
Schema::create('categories', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->timestamps();
});
```

### Bank Accounts Schema

#### Description
The `bank_accounts` table stores information about users' bank accounts. This allows users to import transactions from different bank accounts.

#### Schema Considerations
- Each bank account is linked to a user through `user_id`.
- `bank_name` and `account_number` store the bank's name and the user's account number, respectively.

```php
Schema::create('bank_accounts', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
    $table->string('bank_name');
    $table->string('account_number');
    $table->string('descriprion');
    $table->string('expiry_date');
    $table->timestamps();
});
```
