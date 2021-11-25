<h2>MONGOOSE</h2>

<h3>Connect DB</h3>

```javascript
const mongoose = require("mongoose");

mongoose.connect(
  "url",
  () => {
    console.log("conneted");
  },
  (e) => {
    console.log(e.message);
  }
);
```

<h3>address scema</h3>

```javascript
const addressScema = new mongoose.Schema({
  street: String,
  city: String,
});
```

<h3>user scema</h3>

```javascript
const userScema = new mongoose.Schema({
  name: String,
  age: {
    type: Number,
    min: 10,
    max: 100,
    validate: {
      validator: (v) => v % 2 === 0,
      message: (props) => `${props.value} != even number`,
    }, //can only be used for .save()
  },
  email: {
    type: String,
    required: true,
    lowercase: true, //automatic lowercase
    minLength: 10,
  },
  createdAt: {
    type: Date,
    immutable: true, //can't change
    default: () => Date.now(),
  },
  updatedAt: {
    type: Date,
    default: () => Date.now(),
  },
  bestFriend: {
    type: mongoose.SchemaTypes.ObjectId,
    ref: "User", //reference
  },
  hobbies: [String],
  address: addressScema,
});
```

<h3>add method in scema</h3>

```javascript
userScema.methods.sayHi = function () {
  console.log(`Hi my name is ${this.name}`);
};
```

<h3>add static method scema</h3>

```javascript
userScema.statics.findByName = function (name) {
  return this.where({ name: new RegExp(name, "i") });
};
```

<h3>add query scema</h3>

```javascript
userScema.query.byName = function (name) {
  return this.where({ name: new RegExp(name, "i") });
};
```

<h3>add virtual scema</h3>

```javascript
userScema.virtual("namedEmail").get(function () {
  return `${this.name} <${this.email}>`;
});
```

<h3>middleware</h3>

```javascript
// .pre = before; .post = after
userScema.pre("save", function (next) {
  //run before save()
  this.updatedAt = Date.now();
  next();
});

userScema.pre("remove", function (next) {
  //run before remove()
  console.log(`removed`);
  next();
});

userScema.pre("validate", function (next) {
  //run before validate()
  console.log(`validated`);
  next();
});

userScema.post("save", function (doc, next) {
  doc.sayHi();
  next();
});
```

<h3>create model</h3>

```javascript
const userModel = mongoose.model("User", userScema);
```

<h3>add v1</h3>

```javascript
const user = new userModel({
  name: "Arfian",
  age: 10,
});
await user.save();
console.log(user);
```

<h3>add v2</h3>

```javascript
const user = await userModel.create({
  name: "Arfian",
  age: 10,
});
console.log(user);
```

<h3>change</h3>

```javascript
user.name = "RP";
await user.save();
console.log(user);
```

<h3>add v3</h3>

```javascript
const user = await userModel.create({
  name: "Arfian",
  age: 10,
  hobbies: ["swimming", "reading"],
  address: {
    street: "Minees",
    city: "Birmingham",
  },
});
console.log(user);
```

<h3>find by id</h3>

```javascript
const user = await userModel.findById("id");
console.log(user);
```

<h3>find delete update etc</h3>

```javascript
const user = await userModel.find({ name: "Arfian" }); // userModel.findOne({}):[]; userModel.exists({}):boolean;
console.log(user);
```

<h3>query</h3>

```javascript
const user = await userModel.where("name").equals("Arfian").where("age").gt(10).limit(2).select("name");
console.log(user);
```

<h3>add bestfriend</h3>

```javascript
const user = await userModel.where("name").equals("Arfian").where("age").gt(10).limit(1).select("name");
user[0].bestFriend = "idBestFriend";
await user[0].save();
console.log(user);
```

<h3>get data bestfriend (populate)</h3>

```javascript
const user = await userModel.where("name").equals("Arfian").where("age").gt(10).populate("bestFriend").limit(1);
console.log(user);
```

<h3>call method scema</h3>

```javascript
const user = await userModel.findById("id");
user.sayHi();
```

<h3>call static method scema</h3>

```javascript
const user = await userModel.findByName("Arf");
console.log(user);
```

<h3>call query method scema</h3>

```javascript
const user = await userModel.find().byName("Arf");
console.log(user);
```

<h3>call virtual scema</h3>

```javascript
const user = await userModel.find({ name: "Arfian" });
console.log(user.namedEmail);
```
