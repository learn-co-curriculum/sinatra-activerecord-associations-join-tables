# Sinatra Active Record Associations: Join Tables

By now, you're familiar with the basic Active Record associations: `has_many` and `belongs_to`. But how would you structure the models for an e-commerce site? There would probably be a `User` class and also an `Item` class to cover all of the items a user could buy. A user could select to purchase many items, so your first instinct might be to store `user_id` as a column of the `Items` table. But in an online store, many users could select to buy the same item, so you would need to store more than one `user_id` for each item. Clearly, that option wouldn't work. Basically, we're dealing with a many-to-many relationship. A user can have many items, and an item can belong to many users.

This is where **Join Tables** come into play, with the `has_many through` association, which is used for object associations where more than one object can own many objects of the same class.

In the owner and pet `has_many` and `belongs_to` relationship, we store the `owner_id` on the `pets` table. We use the foreign key to store our relationship.

A join table is a table that only has two columns. In the case of the online store, the columns would probably be `user_id` and `item_id`, with each row representing a specific item that a specific user has decided to purchase. The join tabled should be named according to the two tables it's joining –– in this case, something like `user_items`. The `has_many through` relationship is always singular and in the first part of the join table name, and the `belongs_to` portion of the relationship is pluralized in the second part of the table name.

### Migrations

First, we need to create three migrations: one for the `users` table, one for the `items` table, and one for the join table –– `user_items`.

```ruby
class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      t.string :name
      t.timestamps null: false
    end
end

class CreateItems < ActiveRecord::Migration
  def change
    create_table :items do |t|
      t.string :name
      t.integer :price
      t.timestamps null: false
    end
  end
end
 
class CreateUserItems < ActiveRecord::Migration
  def change 
    create_table :user_items do |t|
      t.integer :user_id
      t.integer :item_id
      t.timestamps null: false
    end
  end
end
```

In these migrations, we create three tables with columns. You'll notice the last table is our join table. The `user_items` table has two columns, `user_id` and `item_id`.

### Models

Next we need to define the appropriate relationships in our three models.

```ruby
class User < ActiveRecord::Base
  has_many :user_items
  has_many :items, through: :user_items
end

class Item < ActiveRecord::Base
  has_many :user_items
  has_many :users, through: :user_items
end

class UserItem < ActiveRecord::Base 
  belongs_to :user
  belongs_to :item
end
```

Our `User` class has two associations: the first is a `has_many` association with the join table, and the second is the `has_many through` association to connect users and items.

We set up a similar pattern of relationships for the `Item` class, this time to connect items to users.

And, finally, we have the model for the join table, `UserItem`. In this model, we set up a `belongs_to` for both users and items.

### Active Record Methods

This gives us access to the users who have purchased a particular item, as well as all the items purchased by a specific user.

<p data-visibility='hidden'>View <a href='https://learn.co/lessons/sinatra-activerecord-associations-join-tables' title='Sinatra Active Record Associations: Join Tables'>Sinatra Active Record Associations: Join Tables</a> on Learn.co and start learning to code for free.</p>
