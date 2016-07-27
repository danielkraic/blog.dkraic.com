+++
date = "2016-05-27T10:34:00+02:00"
title = "CPP variant"
tags = [ "dev", "cpp" ]
+++

# Variants
* resembles union
* able to store values of different types in a boost::variant variable
* at any point only one value can be stored
* When a new value is assigned, the old value is overwritten

```cpp
boost::variant<double, char, std::string> v;
v = 3.14;
std::cout << boost::get<double>(v) << '\n';
v = 'A';
std::cout << boost::get<char>(v) << '\n';
v = "Boost";
std::cout << boost::get<std::string>(v) << '\n';
```

## Motivation

### Example 1

* before

```cpp
class PersonId {
public:
  std::string getName();
  int getJohnDoeId();
  // etc.
private:
  bool m_hasName;
  std::string m_name;
  int m_johnDoeId;
};
```

* after

```cpp
using PersonId = variant<std::string, int>;
```

### Example 2

* before

```cpp
struct command {
  enum type { SET_SCORE, FIRE_MISSILE, FIRE_LASER, ROTATE };
  virtual type getType();
};
struct set_score : commmand {
  type getType() { return SET_SCORE; } override final;
  double value;
};
```

* after

```cpp
struct set_score {
  double value;
};
using command = variant<set_score, fire_missile, fire_laser, rotate>;
```

## Timeline

* 2004 - Boost.Variant released in Boost 1.31.0.
* 2016 - std::variant recommended for C++17 by LEWG.
* 2020 - Language-based variants in C++20?

## boost::variant

#### Creation and Assignment

```cpp
// Default constructs to first alternative.
boost::variant<std::string, int> v;
// Assignment to alternative types.
v = 3;
v = "Hello World";
```

#### Extract a Value with get

```cpp
void output(const boost::variant<std::string, int>& v)
{
  if(std::string const * const s = boost::get<std::string>(&v))
    std::cout << "I got a string: " << *s << std:endl;
  else
    std::cout << "I got an int: " << boost::get<int>(v) << std:endl;
}
```

#### Extract a Value with a visitor

```cpp
struct OutputVisitor
{
  using result_type = void;
  void operator()(const std::string& s) const {
    std::cout << "I got a string: " << s << std:endl;
  }
  void operator()(const int i) const {
    std::cout << "I got an int: " << i << std:endl;
  }
};
void output(const boost::variant<std::string, int>& v)
{
  boost::apply_visitor(OutputVisitor(), v);
}
```

## std::variant

#### Change 1: apply_visitor renamed

```cpp
struct OutputVisitor
{
  void operator()(const std::string& s) const {
    std::cout << "I got a string: " << s << std:endl;
  }
  void operator()(const int i) const {
    std::cout << "I got an int: " << i << std:endl;
  }
};
void output(const std::variant<std::string, int>& v)
{
  std::visit(OutputVisitor(), v);
}
```

```cpp
void output(const std::variant<std::string, int>& v)
{
  return std::visit(
    std::overload(
      [](const std::string & s) {  
        std::cout << "I got a string: " << s << std:endl;
      },
      [](const int i) {  
        std::cout << "I got an int: " << i << std:endl;
      } ),
    v );
}
```

#### Change 2: get reworked

```cpp
void output(const std::variant<std::string, int>& v)
{
  if(std::string const * const s = std::get_if<std::string>(&v))
    std::cout << "I got a string: " << *s << std:endl;
  else
    std::cout << "I got an int: " << std::get<int>(v) << std:endl;
}
```

#### Change 3: index-based access.

```cpp
void output(const std::variant<std::string, int>& v)
{
  if(std::string const * const s = std::get_if<0>(&v))
    std::cout << "I got a string: " << *s << std:endl;
  else
    std::cout << "I got an int: " << std::get<1>(v) << std:endl;
}
```

## Language support for variant.

```cpp
lvariant command {
  std::size_t    set_score;    // Set the score to value.
  std::monostate fire_missile; // Fire a missile.
  unsigned       fire_laser;   // Fire a laser with the specified
                               // intensity.
  double         rotate;       // Rotate the ship by the specified
                               // degrees.
};
```

```cpp
lvariant json_value {
  std::map<std::string, json_value> object;
  std::vector<json_value> array;
  std::string string;
  double number;
  bool boolean;
  std::monostate null_;
};
```

#### Creation of Alternatives

```cpp
// Create a new command 'cmd' that sets the score to '10'.
command cmd = command::set_score( 10 );
```

#### Basic Pattern Matching

```cpp
// Output a human readable string corresponding to the specified 'cmd'
// command to the specified 'stream'.
std::ostream& operator<<( std::ostream& stream, const command cmd ) {
  inspect( cmd ) {
    set_score value =>
      stream << "Set the score to " << value << ".\n";
    fire_missile m =>
      stream << "Fire a missile.\n";
    fire_laser intensity =>
      stream << "Fire a laser with " << intensity << " intensity.\n";
    rotate degrees =>:
      stream << "Rotate by " << degrees << " degrees.\n";
  }
}
```
