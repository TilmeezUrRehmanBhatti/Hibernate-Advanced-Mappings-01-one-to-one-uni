# Hibernate-Advanced-Mappings-01-one-to-one-uni
One-to-One Mapping


**Advanced Mappings Overview**
+ In the database, most likely will have
    + Multiple Tables
    + Relationships between Tables

+ Need to model with Hibernate

+ One-to-One
+ One-to-Many, Many-to-One
+ Many-to-Many

**One-to-One Mapping**
+ An instructor can have an "instructor details" entity
    + Similar to an "instructor profile"

<img src="https://user-images.githubusercontent.com/80107049/188287824-1b42ae27-8714-4135-a7a1-b13a522aeb91.png" width = 600 />


**One-to-Many Mapping**
+ An instructor can have many courses


<img src="https://user-images.githubusercontent.com/80107049/188287842-c012e366-381e-4a91-b711-a565ac7cafe6.png" width = 600 />


**Many-to-many Mapping**
+ A course can have many students
+ A student can have many courses

<img src="https://user-images.githubusercontent.com/80107049/188287848-8a0db399-30a3-4af6-ab19-7047d40a8f44.png" width = 600 />


### Important Database Concept

+ Primary Key and foreign key
+ Cascade

**Primary Key and Foreign Key**
+ Primary key: identify a unique row in a table

<br>

+ Foreign Key:
    + Link tables together
    + a field in one table that refers to primary key in another table


<img src="https://user-images.githubusercontent.com/80107049/188287856-9369da62-d068-4ef2-9a0a-46e08b8d7392.png" width = 600 />

**Cascade**
+ Can **cascade** operations
+ Apply the same operation to related entities

<img src="https://user-images.githubusercontent.com/80107049/188287878-19bcb116-b74b-472f-a19b-28f8f816f5f7.png" width = 200 />

+ If we delete an **instructor**, we should also delete their **instructor_detail**
    + This is known as "CASCADE DELETE

<img src="https://user-images.githubusercontent.com/80107049/188287902-a0514260-eac5-4e23-9895-1703ac4e2680.png" width = 200 />
+ Cascade delete depends on the use case 

**Fetch Types: Eager vs Lazy Loading**
+ When we fetch / retrieve data, should we retrieve EVERYTHING?
    + **Eager** will retrieve everything
    + **Lazy** will retrieve on request


### One-to-One mapping

**Development Process: One-to-One**
1. Pre Work -Define database tables
2. Create InstructorDetail class
3. Create instructor class
4. Create Main App

**Step 1:Define database tables**

_table:instructor_detail_
```POSTGRESQL
CREATE TABLE instructor_detail(
    id              BIGSERIAL NOT NULL,
    youtube_channel VARCHAR(45) DEFAULT NULL,
    hobby           VARCHAR(45) DEFAULT NULL,
    PRIMARY KEY (id)

);
```

_table:instructor_
```POSTGRESQL
CREATE TABLE instructor(
    id                   BIGSERIAL NOT NULL,
    first_name           VARCHAR(45) DEFAULT NULL,
    last_name            VARCHAR(45) DEFAULT NULL,
    email                VARCHAR(45) DEFAULT NULL,
    instructor_detail_id BIGINT      DEFAULT NULL,
    PRIMARY KEY (id),
    CONSTRAINT FK_DETAILS FOREIGN KEY (instructor_detail_id)
        REFERENCES instructor_detail (id)
);
```

**More on Foreign Key**
+ Main purpose is to preserve relationship between tables
    + Referential Integrity

+ Prevents operation that would destroy relationship
+ Ensures only valid data is inserted into the foreign key column
    + Can only contain valid reference to primary key in other table


**Step 2:Create InstructorDetail class**


**Step 3:Create Instructor class**


**Step 3:Create Instructor - @OneToOne**


**Entity Lifecycle**

Every Hibernate entity naturally has a lifecycle within the framework – **it's either in a transient, managed, detached or deleted state**. Understanding these states on both conceptual and technical level is essential to be able to use Hibernate properly.

| Operations  | Description                                                                        |
| ----------- | ---------------------------------------------------------------------------------- |
| **Detach**  | If entity is detached it is not associated with a Hibernate session                |
| **Merge**   | If instance is detached from session, then merge will reattach to session          |
| **Persist** | Transitions new instances to managed state. Next flush / commit will save in db.   |
| **Remove**  | Transitions managed entity to be removed. Next flush / commit will delete from db. |
| **Refresh** | Reload/synch object with data from db. Prevents stale data                         |


**Entity Lifecyle - session method calls**

<img src="https://user-images.githubusercontent.com/80107049/188287916-780d66ff-d970-43b1-a0bd-7b2d152cf563.png" width = 600 />

**@OneToOne - Cascade Types**

| Cascade Type | Description                                                                                  |
| ------------ | -------------------------------------------------------------------------------------------- |
| **PERSIST**  | If entity is persist / saved, related entity will also be persisted                          |
| **REMOVE**   | if entity is removed / deleted, related entity will also be deleted                          |
| **REFRESH**  | If entity is refreshed, related entity will also be refreshed                                |
| **DETACH**   | If entity is detached (not associated w/ session), then related entity will also be detached |
| **MERGE**    | If entity is merged, then related entity will also be merged                                 |
| **ALL**      | All of above cascade types                                                                   |


**Configure Cascade Type**

```JAVA
@Entity
@Table(name="instructor")
public class Instructor {
  ...
    
  @OneToOne(cascade=CascadeType.ALL)
  @JoinColumn(name="instructor_detail_id")
  private InstructorDetail instructorDetail;
  
  ...
  // constructor, getters / setters
}
```
+ By default, no operations are cascaded.

**Configure Multiple Cascade Types**
```JAVA
@OneToOne(cascade={CascadeType.DETACH,
                   CascadeType.MERGE,
                   CascadeType.PERSIST,
                   CascadeType.REFRESH,
                   CascadeType.REMOVE})
```

**Step 4:Create Main App**
```JAVA
public static void main(String[] args) {
  
  ...
  // create the object
  Instructor tempInstructor = new Instructor("Chad", "Darby", "darby@gmail.com");
  
  Instructor tempInstructorDetail = 
    new InstructorDetail("http://www.youtube.com/abcd", "Coding");
  
  // associate the objects
  tempInstructor.setInstructorDetails(tempInstructorDetail);
  
  // start a transactiom
  session.beginTransaction();
  
  session.save(tempInstructor);
  
  // commit transaction
  session.getTransaction().commit();
  
  ...
}
```
