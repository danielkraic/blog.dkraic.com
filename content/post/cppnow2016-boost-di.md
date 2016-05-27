# Boost::DI

* C++14 header only Dependency Injection library with no dependencies

## Dependency Injection (DI)
* involves passing (injecting) one or more dependencies (or services) to a dependent object (or client) which become part of the client’s state.

## Example

### No dependency injection
```cpp
class coffee_maker {
public:
  // create dependencies in the constructor
  coffee_maker()
  : heater(std::make_shared<electric_heater>())
  , pump(std::make_unique<heat_pump>(heater))
  { }
  void brew() {
    heater->on();
    pump->pump();
  }
private:
  std::shared_ptr<iheater> heater;
  std::unique_ptr<ipump> pump;
};
```

### Dependency injection
```cpp
class coffee_maker {
public:
  // inject dependencies via constructor
  coffee_maker(std::shared_ptr<iheater> heater, std::unique_ptr<ipump> pump)
  : heater(heater), pump(std::move(pump))
  { }
  void brew() {
    heater->on();
    pump->pump();
  }
private:
  std::shared_ptr<iheater> heater;
  std::unique_ptr<ipump> pump;
};
```

### Motivation

* DI provides loosely coupled code (separation of business logic and object creation)
* DI provides easier to maintain code (different objects might be easily injected)
* DI provides easier to test code (fakes objects might be injected)

## Examples

#### Example 1

* app
```cpp
struct renderer { int device; };
class iview {
public:
  virtual ~iview() = default;
  virtual void update() = 0;
};
class model {};
class controller {
public:
  controller(model&, view&) {}
};
class user {};
class app {
public:
  app(controller&, user&) {}
};
```

* usual approach to create app
```cpp
renderer renderer_;
view view_{"title", renderer_};
model model_;
controller controller_{model_, view_};
user user_;
app app_{controller_, user_};
```

* create app with boost::di
```cpp
auto app = di::make_injector().create<app>();
```

#### Example 2

```cpp
class controller;

struct renderer {
  int device;
};

class view {
 public:
  view(std::string /*title*/, const renderer&) {}
};

class model {};
class user {};

class app {
 public:
  app(controller&, user&) {}
};

/// CREATE OBJECTS TREE
class controller {
 public:
  controller(model&, view&) {}
};
```

```cpp
int main() {
  /// NO DEPENDENCY INJECTION
  {
    renderer renderer_;
    view view_{"", renderer_};
    model model_;
    controller controller_{model_, view_};
    user user_;
    app app_{controller_, user_};
    (void)app_;
  }

  /// DEPENDENCY INJECTION
  {
    auto injector = di::make_injector();
    auto app_ = injector.create<app>();
    (void)app_;
  }
}
```

```cpp
/// CHANGE CONTROLLER CONSTRUCTOR ORDER
class controller {
 public:
  controller(view&, model&) {}
};

int main() {
  /// NO DEPENDENCY INJECTION
  {
  renderer renderer_;
  view view_{"", renderer_};
  model model_;
  //controller controller_{model_, view_};
  controller controller_{view_, model_}; /// CHANGE
  user user_;
  app app_{controller_, user_};
  (void)app_;
  }

  /// DEPENDENCY INJECTION
  {
  auto injector = di::make_injector();
  auto app_ = injector.create<app>();
  (void)app_;
  }
}
```

```cpp
/// ADD A NEW DEPENDENCY TO CONTROLLER
struct configuration {};
class controller {
 public:
  controller(view&, model&, configuration) {}
};

int main() {
  /// NO DEPENDENCY INJECTION
  {
  renderer renderer_;
  view view_{"", renderer_};
  model model_;
  //controller controller_{model_, view_};
  controller controller_{view_, model_, configuration{}}; /// CHANGE
  user user_;
  app app_{controller_, user_};
  (void)app_;
  }

  /// DEPENDENCY INJECTION
  {
  auto injector = di::make_injector();
  auto app_ = injector.create<app>();
  (void)app_;
  }
}
```

### Bindings

* interfaces

```cpp
class iview {
public:
  virtual ~iview() noexcept = default;
  virtual void update() =0;
};

class gui_view: public iview {
public:
  gui_view(std::string title, const renderer&) {}
  void update() override {}
};

class text_view: public iview {
public:
  void update() override {}
};

auto injector = di::make_injector(
  di::bind<iview>.to<gui_view>() // bind interface to implementation
);
```

```cpp
struct T { // create using uniform initialization
  int& a;  // might be used to serialize
  double b;
};

auto i = 42;
auto injector = di::make_injector(
  di::bind<int>.to(i),
  di::bind<double>.to(87.0)
);
injector.create<T>(); // will create T{i, 87.0};
```

```cpp
auto use_gui_view = true/false;

auto injector = di::make_injector(
  di::bind<iview>.to([&](const auto& injector) -> iview& {
    return use_gui_view ?
      injector.template create<gui_view&>() :
      injector.template create<text_view&>();
  })
);

use_gui_view = true;
assert(dynamic_cast<gui_view*>(
  injector.create<std::unique_ptr<iview>().get())
);

use_gui_view = false;
assert(dynamic_cast<text_view*>(
  injector.create<std::unique_ptr<iview>().get())
);
```
