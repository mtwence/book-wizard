# "Book Wizard"

## Table of Contents

- [Description](#description)
- [Installation](#installation)
- [Technologies](#technologies-used)
- [Deployed Link](#link)
- [Usage](#usage)
- [User Information](#user-information)
- [Credits](#credits)
- [Questions](#questions)
- [License](#license)

## Description

This book search engine was overhauled to utilize Apollo as a wrapper for graphql for our server side CRUD actions, which ties into React for the client side. A logged in user is able to searh, save and remove books from their favorites list.

## Installation

```ruby
npm install
```

## Technologies Used

-MongoDB
-Apollo
-GraphQL
-Express
-Node
-React

## Deployed Link

[alt text](https://book-wizard.herokuapp.com/)

## Usage

### Code Snippets

TypeDefs for GraphQL.

```javascript
const typeDefs = gql`
  type User {
    _id: ID!
    username: String
    email: String
    password: String
    bookCount: Int
    savedBooks: [Book]
  }
  type Book {
    _id: ID!
    authors: [String]
    description: String
    bookId: String
    image: String
    link: String
    title: String
  }
  input savedBook {
    description: String
    title: String
    bookId: String
    image: String
    link: String
    authors: [String]
  }
  type Auth {
    token: ID!
    user: User
  }
  type Query {
    me: User
  }
  type Mutation {
    login(email: String!, password: String!): Auth
    addUser(username: String!, email: String!, password: String!): Auth
    saveBook(input: savedBook!): User
    removeBook(bookId: ID!): User
  }
`;
```

Here are the GraphQL resolvers.

```javascript
const resolvers = {
  Query: {
    me: async (parent, args, context) => {
      if (context.user) {
        return User.findOne({
          _id: context.user._id,
        });
      }
      throw new AuthenticationError("You need to be logged in!");
    },
  },

  Mutation: {
    addUser: async (parent, { username, email, password }) => {
      const user = await User.create({ username, email, password });
      const token = signToken(user);

      return { token, user };
    },

    login: async (parent, { email, password }) => {
      const user = await User.findOne({ email });

      if (!user) {
        throw new AuthenticationError("No user with this email found");
      }
      const correctPw = await user.isCorrectPassword(password);

      if (!correctPw) {
        throw new AuthenticationError("Incorrect Password");
      }
      const token = signToken(user);
      return { token, user };
    },

    saveBook: async (parent, book, context) => {
      if (context.user) {
        const updateBook = await User.findOneAndUpdate(
          { _id: context.user._id },
          { $push: { savedBooks: book.input } },
          { new: true }
        );
        return updateBook;
      }
      throw new AuthenticationError("Log in to save book");
    },

    removeBook: async (parent, book, context) => {
      if (context.user) {
        const data = await User.findOneAndUpdate(
          { _id: context.user._id },
          { $pull: { savedBooks: { bookId: book.bookId } } },
          { new: true }
        );
        return data;
      }
      throw new AuthenticationError("You need to be logged in!");
    },
  },
};
```

## User Information

### **Michael Wence**

[LinkedIn](https://www.linkedin.com/in/michael-wence/) |
[GitHub](https://github.com/mtwence)

## Credits

UCB - Coding Bootcamp

## Questions

If you have any questions about this repository, you can contact me at mtwence@gmail.com.

## License

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

© 2022 Michael Wence. All Rights Reserved.
