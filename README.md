# Sinatra ActiveRecord Associations: Join Tables

By now, you're familiar with the basic ActiveRecord associations: `has_many` and `belongs_to`. But how would you handle an e-commerce site? If you think about the models, there would be a `User` class and an `Item` class, which would contain all the items a user could buy. A user could select to purchase many items, so your first instinct might be to store `user_id` as a column of the `Items` table. But with an online store, many many users could select to buy the same item, so you would need to store more than one `user_id`. Clearly that option wouldn't work. Basically, we're dealing with a man-to-many relationship. A user can have many items, and an item can belong to many users.

This is where **Join Tables** come to play, with the `has_many :through` association, which is used for object associations where more than one object can own many objects of the same class. 

In the owner and pet `has_many` and `belongs_to` relationship, we store the `owner_id` on the `pets` table. We use the foreign key to store our relationship .

A join table is a table that only has two columns. To keep up with the online store example, this table would contain a `user_id` and `item_id`. Each row in this table would contain a user's ID and an item's ID. We call this join table `user_items`. The `has_many :through` is always the singular and in first part of the join table name, and the `belongs_to` portion of the relationship is pluralized in the second part of the table name.


### Migrations

First, we need to create three migrations: one for the users table, one for the items table, and the join table -- user_items.

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

In these migrations, we create three tables with columns. You'll notice the last table is our join table. The `user_items` table has two columns `user_id` and `item_id`.

### Models

Next we need to define the appropriate relationships in our three models.

```ruby
class User < ActiveRecord::Base
  has_many :user_items
  has_many :items, through: user_items
end

class Item < ActiveRecord::Base
  has_many :user_items
  has_many :users, through: user_items
end

class UserItem < ActiveRecord::Base 
  belongs_to :user
  belongs_to :item
end
```

Our `User` class has two associations, the first is a `has_many` association with the join table, and the second is the `has_many through:` with to connect users and items.

We set up a similar pattern of relationships for the `Item` class, this time to connect items to users.

And finally, we have the model for the join table, `UserItem`. In this model, we set up a `belongs_to` for both users and items.

### ActiveRecord Methods 

This gives us access to the users who have purchased a particular item, as well as all the items purchased by a specific user.



<a href='https://learn.co/lessons/sinatra-activerecord-associations-readme' data-visibility='hidden'>View this lesson on Learn.co</a>

<p data-visibility='hidden'>View <a href='https://learn.co/lessons/sinatra-activerecord-associations-join-tables' title='Sinatra ActiveRecord Associations: Join Tables'>Sinatra ActiveRecord Associations: Join Tables</a> on Learn.co and start learning to code for free.</p>
