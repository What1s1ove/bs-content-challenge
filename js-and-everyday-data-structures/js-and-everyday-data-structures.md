# JS and everyday data structures

Let's start with a quote that once changed my life. I am sure that this quote and its understanding will also change yours, if you have not heard about it yet.

> Bad programmers worry about the code. Good programmers worry about data structures and their relationships. &copy; [Linus Torvalds](https://en.wikipedia.org/wiki/Linus_Torvalds)

We will talk about data structures, but it is impossible to talk about them without mentioning algorithms. So let's talk about both things.

Algorithm is the steps we need to take to solve a problem. Data structures are organized data with efficient and convenient access to them.

Out of the box JS has two data structures — `Array` and `Hash Table` (`Object`, `Set`, `Map`). Not much, but based on these two, it is possible to build almost any other structures (`Queue`, `Enum`, `Linked List`, `Graphs` and etc.).

Let's get started. We will start with the `Linked List`.

**`Linked List`** — the data structure that consists of a group of nodes which... wait, wait, wait. I am sure `Linked List` is a great structure that solves the problems, but today I want to talk about `simpler`, `useful` and `everyday` structures that will make your life easier. Namely about `Enum` and `Map`.

We will talk about these two data structures, look at the conventions that JS community has about them: how to call them, how to compose them and how they help us solve our everyday problems.

(Also, If you want to know more about the other data structures that I mentioned above (hope so) but which we will not talk about today, you can take a look at [this](https://medium.com/free-code-camp/10-common-data-structures-explained-with-videos-exercises-aaff6c06fb2b) article. I am sure that the guys from [freeCodeCamp](https://www.freecodecamp.org/) explain these structures better than me 🙂)

Program for today:
 - CONSTANTs
 - Enum
 - Map
 - Enum (TS)
 - Enum TC39 proposals
 - Conclusions

## Let's get started! 🔥

We will use this user type for examples:

```ts
type User = {
  name: string;
  gender: string;
}
```

Do not worry if you are not familiar with [TypeScript](https://www.typescriptlang.org/). The example of a type is given so that you understand the approximate structure of the user.

## The Task

Let's imagine that we have a task — we need to display a list of users using the appropriate emoji by the user's gender. For now we have only two options — `male` and `female`.

It might look like this:

```jsx
// src/components/common/users-list/users-list.jsx

<ul>
  {users.map((it) => (
    <li>
      <h3>{it.name}</h3>
      <p>{it.gender === 'male' ? '👨' : '👩'}</p>
    </li>
  ))}
</ul>
```

That's it. The task is done. Buuut... we can do it *better*.

Let's imagine that in our code, there will appear, or there are already places where we check something by the user's gender. We duplicate the code. It is not good. Also, if we all the time will use *just a string*, sooner or later, we will make a mistake. In fact, such errors are not very easy to find.

Let's solve this problem too. We will protect ourselves from this. We will use a *single source of truth* — **`CONSTANT`**'s.

## CONSTANT

`CONSTANT`s are used to store data that is known *before the start* of the program and that *should not be changed* during the execution of the program. It is important that the constant is not the `const` keyword for variables. The constant can be declared with any keyword for variables (`var`, `let`, `const`), but `const` is commonly used.

Constants are used not only in JavaScript, and there are some conventions for them:

- Constant must be declared at the top of the program/module (after imports, if any);
- Constant must have a name in capital letters;
- Constant must not be redefined anywhere in the program.

<details>
  <summary>Examples</summary>

  ```jsx
    // bad

      // src/common/constants/earth/index.js

      const earthRadius = 6371;

      // src/components/common/user-item/user-item.jsx

      const UserItem = ({ user }) => {
        const USER_ROLE = getUserRole(user);

        return <li>{user.name}, {USER_ROLE}</li>
      }

      // src/components/dashboard/dashboard.jsx

      let USER_TYPE = 'user';

      if (checkIsAdmin(user)) {
        USER_TYPE = 'admin';
      }

    // good

      // src/common/constants/earth/index.js

      const EARTH_RADIUS = 6371;

      // src/components/common/user-item/user-item.jsx

      const UserItem = ({ user }) => {
        const userRole = getUserRole(user);

        return <li>{user.name}, {userRole}</li>
      }

      // src/components/dashboard/dashboard.jsx

      let userType = 'user';

      if (checkIsAdmin(user)) {
        userType = 'admin';
      }
  ```

</details>

Here is an improved solution for our task:

```jsx
// src/common/constants/user/index.js

const MALE_GENDER_TYPE = 'male';
const FEMALE_GENDER_TYPE = 'female';

// src/components/common/users-list/users-list.jsx

<ul>
  {users.map((it) => (
    <li>
      <h3>{it.name}</h3>
      <p>{it.gender === MALE_GENDER_TYPE ? '👨' : '👩'}</p>
    </li>
  ))}
</ul>
```

Much better, but we can also improve it.

Have you noticed that we duplicate `GENDER_TYPE` in the name? It is not critical, but annoying 🙃. Also, if we needed some values, we would have to import each constant separately.

It would be cool if there was a data structure that would help us with this as well. And there is such a structure — **`Enum`**.

## Enum

`Enum` is a date structure that is used as a set of constants.

For this structure, JavaScript also has conventions among developers:

- Enum must start with a capital letter;
- Enum must be singular;
- Enum must have uppercase keys;
- Enum must not be changed anywhere in the program.

<details>
  <summary>Examples</summary>

  ```jsx
    // bad

      // src/common/enum/user/user-role-enum.enum.js

      const userRoleEnum = {
        user: 1,
        viewer: 2,
      };

      // or

      const userRoles = {
        user: 1,
        viewer: 2,
      };

      // or

      const UserRoles = {
        user: 1,
        viewer: 2,
      }

      if (checkHasAdminRole()) {
        UserRoles.admin = 3
      }

    // good

      // src/common/enum/user/user-role.enum.js

      const UserRole = {
        USER: 1,
        VIEWER: 2,
        ADMIN: 3
      };
  ```

</details>

This is how the solution using the `Enum` might look like:

```jsx
// src/common/enums/user/gender-type.enum.js

const GenderType = {
  MALE: 'male',
  FEMALE: 'female'
};

// src/components/common/users-list/users-list.jsx

<ul>
  {users.map((it) => (
    <li>
      <h3>{it.name}</h3>
      <p>{it.gender === GenderType.MALE ? '👨' : '👩'}</p>
    </li>
  ))}
</ul>
```

We could keep using constants, but it is much better and more correct to use structures that are better suited for this.

Also, if we did the task with TypeScript, we could also change a little the type of the user. This is *many times better* than using just strings.

```ts
type User = {
  name: string;
  gender: typeof GenderType;
}
```

Let's now imagine that a business comes to us and says that they want us to add a new type for the user's gender — 'non-binary'. We could do something like this:

```jsx
// src/components/common/users-list/users-list.jsx

<ul>
  {users.map((it) => (
    <li>
      <h3>{it.name}</h3>
        <p>
          {it.gender === GenderType.MALE
            ? '👨'
            : GenderType.FEMALE
            ? '👩'
            : '🧑'}
        </p>
    </li>
  ))}
</ul>
```

But it looks very ugly. We could replace this with a `switch` statement, or a helper function that does it itself. It would be better, but we can also use special structures for this. We can use a **`Map`** data structure to make the solution more flexible and readable.

## Map

`Map` is a data structure that is used to map some values to others.

There are some conventions how to build this structure:

- must have a name by these patterns — `<someValue>To<someValue>` or `<someValue>Map` (the first option is used more often).

<details>
  <summary>Examples</summary>

  ```jsx
    // bad

      // src/common/maps/user/user-roles.map.js

      const userRoles = {
        user: 'Default User',
        viewer: 'Checker',
        admin: 'Administrator'
      };

    // good

      // src/common/maps/user/user-role-to-readable.map.js

      const userRoleToReadable = {
        user: 'Default User',
        viewer: 'Checker',
        admin: 'Administrator'
      };

      // src/common/maps/user/user-role-map.map.js

      const userRoleMap = {
        user: 'Default User',
        viewer: 'Checker',
        admin: 'Administrator'
      };
  ```

</details>

This is how the solution might look like using the `Map`:

```jsx
// src/common/enums/gender-type.enum.js

const GenderType = {
  MALE: 'male',
  FEMALE: 'female',
  NON_BINARY: 'non-binary'
};

// src/common/maps/gender-type-to-emoji.map.js

const genderTypeToEmoji = {
  [GenderType.MALE]: '👨',
  [GenderType.FEMALE]: '👩',
  [GenderType.NON_BINARY]: '🧑'
};

// src/components/common/users-list/users-list.jsx

<ul>
  {users.map((it) => (
    <li>
      <h3>{it.name}</h3>
      <p>{genderTypeToEmoji[it.gender]}</p>
    </li>
  ))}
</ul>
```

Have you noticed how we reused all the structures we learned about above? 🙂

Using data structures and their combinations is a very *powerful* tool that is very much *appreciated* among developers.

Here are some more examples where using data structures helps a lot:

<details>
  <summary>Select Control</summary>

  ```jsx
  // src/common/maps/gender-type-to-readable.map.js

  const genderTypeToReadable = {
    [GenderType.MALE]: 'Male',
    [GenderType.FEMALE]: 'Female',
    [GenderType.NON_BINARY]: 'Non Binary (NB)'
  };

  // src/components/sign-up/components/register-form/register-form.jsx

  const ganderOptions = getOptions(Object.values(GenderType), (gender) => ({
    label: genderTypeToReadable[gender],
    value: gender
  }))

  <Select label="Gender:" options={ganderOptions} />
  ```

</details>

<details>
  <summary>Tabs</summary>

  ```jsx
  // src/components/dashboard/common/enums/tab-name.enum.js

  const TabName = {
    USERS: 'Users',
    FORM: 'Register Form',
    PERMISSIONS: 'User Permissions'
  };

  // src/components/dashboard/dashboard.jsx

  const tabOptions = getOptions(Object.keys(TabName));

  const Dashboard = () => {
    const [currentTab, setCurrentTab] = React.useState(TabName.USERS);

    const getScreen = (tabName) => {
      switch (tabName) {
        case TabName.USERS: {
          return <User />;
        }
        case TabName.FORM: {
          return <RegisterForm />;
        }
        case TabName.PERMISSIONS: {
          return <Permissions />;
        }
      }

      return null;
    };

    return (
      <>
        <TabList options={tabOptions} onChange={setCurrentTab} />
        <div className="screen-wrapper">{getScreen(currentTab)}</div>
      </>
    );
  };
  ```

</details>

<details>
  <summary>getFilteredOffers</summary>

  ```js
  // src/components/dashboard/common/maps/user-offer-to-validation-cb.map.js

  const userOfferToValidationCb = {
    checkHasCurrentType() { /* checking... */ },
    checkHasCurrentPrice() { /* checking... */ },
    checkHasCurrentRooms() { /* checking... */ }
  };

  // src/components/dashboard/helpers/get-filtered-offers/get-filtered-offers.helper.js

  const getFilteredOffers = (offers) => {
    return offers.filter((offer) => {
      const isSuitable = Object.keys(userOfferToValidationCb).every((key) => {
        const validationFn = userOfferToValidationCb[key];

        return validationFn(offer);
      });

      return isSuitable;
    });
  };
  ```

</details>

Here is an example of the `getOptions` helper:

<details>
  <summary>Example</summary>

  ```js
  // src/helpers/options/get-default-option/get-default-option.helper.js

  const getDefaultOption = (value) => ({
    value,
    label: value
  });

  // src/helpers/options/get-options/get-options.helper.js

  const getOptions = (values, cb = getDefaultOption) => values.map(cb);
  ```

</details>

## TypeScript Enum

TypeScript has a keyword for the `Enum`'s. The key difference from an object-enum is that with the TypeScript enum we can read values in two ways.

```ts
// src/common/enums/gender-type.enum.ts

enum GenderType {
  MALE = 'male',
  FEMALE = 'female',
  NON_BINARY = 'non-binary'
};

console.log(GenderType.MALE) // male
console.log(GenderType['male']) // MALE
```

Cool! But I have almost never seen this used in production code. Therefore, I use and recommend that you use `Enum` in TypeScript in this way:

```ts
// src/common/enums/gender-type.enum.ts

const GenderType = {
  MALE: 'male',
  FEMALE: 'female',
  NON_BINARY: 'non-binary'
} as const;
```

With [const assertions](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-4.html#const-assertions) we still use a regular object, but now it's on steroids.

Moreover, `enum` is [always a reserved word](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Lexical_grammar#future_reserved_keywords) in JavaScript, which means that maybe someday we will have a construct that the language itself will offer us.

Also, none of the programs that compress the JS code cannot compress TS `enum`s. Which cannot be said about the objects that pure Java script offers us.

## Enum TC39 proposals

As I said above, that `Enum` may appear in vanilla JavaScript someday. You can view the proposals that we have at the moment:

- https://github.com/rwaldron/proposal-enum-definitions
- https://github.com/rbuckton/proposal-enum
- https://es.discourse.group/t/enumerations-syntactic-sugar/144

Usually, the solution that the language itself can offer is many times more effective than any third-party library can offer.

Do not forget to check the [proposals](https://github.com/tc39/proposals) that may appear in JavaScript language, and vote for the most interesting for you.

## Conclusions

Data structures are awesome! They help us to solve problems in a beautiful and convenient way. A huge plus is that if you do them according to the conventions that are present in the JavaScript language, most developers will understand many times faster than coming up with something 'new'.

Most of the things have already been done for us 🙂

Do not forget to put the constants of the same type into `Enum`s, then Map them into the format you need, and enjoy the gracefully completed tasks 🔥
