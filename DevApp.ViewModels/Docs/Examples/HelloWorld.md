﻿##### HelloWorld.js

```jsx
import React from "react";
import dotnetify from "dotnetify";
import styled from "styled-components";

const Container = styled.div`
  /*...styles...*/
`;

class HelloWorld extends React.Component {
  constructor(props) {
    super(props);
    this.state = { FirstName: "", LastName: "" };

    // Connect this component to the back-end view model.
    this.vm = dotnetify.react.connect("HelloWorldVM", this);

    // Set up function to dispatch state to the back-end.
    this.dispatchState = state => this.vm.$dispatch(state);
  }

  componentWillUnmount() {
    this.vm.$destroy();
  }

  render() {
    return (
      <Container>
        <section>
          <TextBox
            label="First Name:"
            placeholder="Type first name here"
            value={this.state.FirstName}
            onChange={value => this.setState({ FirstName: value })}
            onUpdate={value => this.dispatchState({ FirstName: value })}
          />
          <TextBox
            label="Last Name:"
            placeholder="Type last name here"
            value={this.state.LastName}
            onChange={value => this.setState({ LastName: value })}
            onUpdate={value => this.dispatchState({ LastName: value })}
          />
        </section>
        <footer>
          Full name is <b>{this.state.FullName}</b>
        </footer>
      </Container>
    );
  }
}

class TextBox extends React.Component {
  state = { changed: false };

  handleChange = event => {
    this.setState({ changed: true });
    this.props.onChange(event.target.value);
  };

  handleBlur = () => {
    if (this.state.changed) this.props.onUpdate(this.props.value);
    this.setState({ changed: false });
  };

  render() {
    return (
      <div>
        <label>{this.props.label}</label>
        <input
          type="text"
          className="form-control"
          value={this.props.value}
          placeholder={this.props.placeholder}
          onChange={this.handleChange}
          onBlur={this.handleBlur}
        />
      </div>
    );
  }
}
```

##### HelloWorldVM.cs

```csharp
public class HelloWorldVM : BaseVM
{
   private string _firstName = "Hello";
   private string _lastName = "World";

   public string FirstName
   {
      get => _firstName;
      set
      {
         _firstName = value;
         Changed(nameof(FullName));
      }
   }
   public string LastName
   {
      get => _lastName;
      set
      {
         _lastName = value;
         Changed(nameof(FullName));
      }
   }
   public string FullName => $"{FirstName} {LastName}";
}
```