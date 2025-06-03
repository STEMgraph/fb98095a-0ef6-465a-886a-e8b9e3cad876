<!---
{
  "id": "fb98095a-0ef6-465a-886a-e8b9e3cad876",
  "depends_on": ["AND", "58818b9a-23fe-4cf2-83f4-08bc3ff1cff5", "a8abf235-1dcb-4234-89bd-b380e96b5378"],
  "author": "Stephan Bökelmann",
  "first_used": "2025-05-20",
  "keywords": ["CopperSpice", "GUI", "C++", "lambda", "QColorDialog"]
}
--->

# Color Picker with Lambda Function 

## Introduction 

> In this exercise you will learn how to use multiple buttons in a CopperSpice application and how to invoke dialogs using lambda expressions. Furthermore, we will examine layout management using spacing and stretching.

This example builds directly upon our minimal GUI setup. We expand the main window with two push buttons: one to trigger a color picker dialog and another to close the application.

The key new element in this example is the use of a **lambda expression** within the `QObject::connect()` call. Lambda functions, introduced in C++11, provide a concise way to define anonymous functions inline. They are especially useful for GUI callbacks where you want to embed a small, one-off function directly where it's used.

Here, we connect the `clicked` signal of a button to a lambda that opens a color selection dialog with a default color set to green. This eliminates the need to define a separate slot function or subclass, making the code cleaner and easier to maintain for simple actions.

We also use layout elements like `addStretch()` and `addSpacing()` to control the visual alignment and padding between widgets, demonstrating basic layout customization.

### Further Readings and Other Sources

* [CopperSpice Journal](https://journal.copperspice.com/)
* [MaxClerkwell’s CopperSpice YouTube Playlist](https://www.youtube.com/playlist?list=PLwW_X4nvqRnxfwo7tXsC3Szu-gc4sv1mp)
* [Empty CopperSpice Project Template](https://github.com/STEMgraph/empty_copperspice_project)

## Tasks

### Task 1: Clone the project template

To accelerate the process, I uploaded an empty project repository, based on the official CopperSpice Journal.
Open a terminal and clone the starter project:

```sh
git clone https://github.com/STEMgraph/empty_copperspice_project.git ~/STEMgraph/experiments/copperspice/ColorPicker
cd ~/STEMgraph/experiments/copperspice/ColorPicker
```

### Task 2: Add new source code

The only file, that you need to modify right now is the `./src/main.cpp`. Open it in your favorite editor, e.g. using `vim`:

```sh
vim ./src/main.cpp
```

Replace the contents with the following:

```cpp
#include <QtCore>
#include <QtGui>

int main(int argc, char *argv[])
{
   QApplication app(argc, argv);

   QWidget *mainWindow = new QWidget();
   mainWindow->setMinimumSize(700, 350);

   QPushButton *open_selector_button = new QPushButton();
   open_selector_button->setText("Show Colors");

   QPushButton *close_button = new QPushButton();
   close_button->setText("Close");

   QHBoxLayout *layout = new QHBoxLayout(mainWindow);
   layout->addStretch();
   layout->addWidget(open_selector_button);
   layout->addSpacing(10);
   layout->addWidget(close_button);
   layout->addStretch();

   QObject::connect(open_selector_button, &QPushButton::clicked,
         open_selector_button, []() { QColorDialog::getColor(Qt::green); });

   QObject::connect(close_button, &QPushButton::clicked,
         mainWindow, &QWidget::close);

   mainWindow->show();

   return app.exec();
}
```

Pay attention to the `::connect` method, that was already used in the minimal example. 
This time we add two buttons. 
Since the need to be somehow placed on the canvas of our window, we also want to add a layouting structure. 
This is done by creating a new layout object, which takes the object `mainWindow` as a construction-parameter.
The constructor of the layout will know, that it is supposed to live __inside__ of this `mainWindow`. 
With the following lines, we are adding objects to this layout from left to right. 
First a stretchable empty object, then our first button, a fixed spacing, the second button and another stretchable empty object. 

### Task 3: Build and Run

Follow the build instructions from the `README.md` in the template repository. Generally, the steps are:

```sh
cmake -B ./build -DCMAKE_PREFIX_PATH=$CS19_LIB_PREFIX
cmake --build ./build
./build/ColorPicker
```

Ensure the `$CS19_LIB_PREFIX` environment variable is correctly set to the CopperSpice installation path.

## Questions

1. What does the lambda in the `connect` call do, and how could you expand it to react to the color selected?
2. Why is `color_selector_button` both the sender and receiver in the lambda connection?
3. What happens if you remove the layout spacing or stretches?
4. How could you reuse this layout code in a more complex application structure?
5. Can lambda expressions capture variables from outside their scope? How would that be used in a GUI context?
6. Describe, which feature the following code adds to the project?
```cpp
#include <QApplication>
#include <QWidget>
#include <QPushButton>
#include <QTextEdit>
#include <QColorDialog>
#include <QVBoxLayout>
#include <QHBoxLayout>

int main(int argc, char *argv[])
{
   QApplication app(argc, argv);

   QWidget *mainWindow = new QWidget();
   mainWindow->setMinimumSize(700, 350);

   QPushButton *color_selector_button = new QPushButton("Select Color");
   QPushButton *close_button = new QPushButton("Close");
   QTextEdit *color_display = new QTextEdit();

   QVBoxLayout *outerLayout = new QVBoxLayout(mainWindow);
   QHBoxLayout *innerLayout = new QHBoxLayout();

   outerLayout->addStretch();
   outerLayout->addWidget(color_display);
   outerLayout->addSpacing(10);
   outerLayout->addLayout(innerLayout);
   outerLayout->addStretch();

   innerLayout->addStretch();
   innerLayout->addWidget(color_selector_button);
   innerLayout->addSpacing(10);
   innerLayout->addWidget(close_button);
   innerLayout->addStretch();

   QColor activeColor = Qt::green;
   color_display->setText(activeColor.name());
   color_display->setReadOnly(true);

   QObject::connect(color_selector_button, &QPushButton::clicked,
         color_selector_button, [&activeColor, color_display]() {
            QColor selectedColor = QColorDialog::getColor(activeColor);
            if (selectedColor.isValid()) {
               activeColor = selectedColor;
               color_display->setText(activeColor.name());
            } // else: do nothing
         });

   QObject::connect(close_button, &QPushButton::clicked,
         mainWindow, &QWidget::close);

   mainWindow->show();
   return app.exec();
}
```

## Advice

Don't worry if lambda syntax or layout spacing feels a bit abstract — it becomes intuitive with practice. Focus on seeing the structure: input (button click) → response (action). Using lambdas keeps your GUI logic local and readable, but for anything more complex, consider defining a separate class. You now know enough to experiment confidently with multiple widgets and dynamic behavior. When you're ready, expand this example with labels, user input fields, or message boxes, or refer to the [next sheet on custom widget signals](https://github.com/STEMgraph/missing) for deeper practice.
