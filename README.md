# Scientific Calculator Using Android Studio

## Aim

To create and design a **Scientific Calculator Android application** using Android Studio to perform basic and scientific mathematical operations.

## Equipment Required

* Android Studio (Latest Version)
* Android SDK
* Android Emulator or Android Device
* Java/XML

## Algorithm

1. Open **Android Studio** and select **File → New → New Project**.
2. Enter the application name as **Scientific Calculator**.
3. Select the required **Minimum SDK** and click **Next**.
4. Select **Empty Activity** and click **Finish**.
5. Design the scientific calculator interface in `activity_main.xml`.
6. Add buttons for numbers and arithmetic operations.
7. Add scientific operation buttons such as:

   * `sin`
   * `cos`
   * `tan`
   * `√`
   * `log`
   * `ln`
   * `x²`
   * `π`
8. Create the required variables and button click listeners in `MainActivity.java`.
9. Read the values entered by the user.
10. Perform the selected arithmetic or scientific operation.
11. Display the calculated result on the calculator screen.
12. Save and run the application using an emulator or Android device.
13. Verify the output for different mathematical operations.

## Program

### `MainActivity.java`

```java
package com.example.scientificcalculator;

import android.os.Bundle;
import android.widget.TextView;

import androidx.activity.EdgeToEdge;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.graphics.Insets;
import androidx.core.view.ViewCompat;
import androidx.core.view.WindowInsetsCompat;

import com.google.android.material.button.MaterialButton;

import net.objecthunter.exp4j.Expression;
import net.objecthunter.exp4j.ExpressionBuilder;
import net.objecthunter.exp4j.operator.Operator;

import java.text.DecimalFormat;

public class MainActivity extends AppCompatActivity {

    private TextView formulaText;
    private TextView resultText;
    private final StringBuilder formula = new StringBuilder();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        EdgeToEdge.enable(this);
        setContentView(R.layout.activity_main);
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main), (v, insets) -> {
            Insets systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars());
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom);
            return insets;
        });

        formulaText = findViewById(R.id.formula_text);
        resultText = findViewById(R.id.result_text);

        setClickListeners();
    }

    private void setClickListeners() {
        int[] digitIds = {
                R.id.btn_0, R.id.btn_1, R.id.btn_2, R.id.btn_3, R.id.btn_4,
                R.id.btn_5, R.id.btn_6, R.id.btn_7, R.id.btn_8, R.id.btn_9,
                R.id.btn_dot, R.id.btn_plus, R.id.btn_minus, R.id.btn_open_paren, 
                R.id.btn_close_paren, R.id.btn_power, R.id.btn_pi, R.id.btn_e
        };

        for (int id : digitIds) {
            findViewById(id).setOnClickListener(v -> {
                MaterialButton button = (MaterialButton) v;
                formula.append(button.getText().toString());
                updateDisplay();
            });
        }

        findViewById(R.id.btn_multiply).setOnClickListener(v -> {
            formula.append("x");
            updateDisplay();
        });

        findViewById(R.id.btn_divide).setOnClickListener(v -> {
            formula.append("÷");
            updateDisplay();
        });

        findViewById(R.id.btn_sin).setOnClickListener(v -> appendFunction("sin("));
        findViewById(R.id.btn_cos).setOnClickListener(v -> appendFunction("cos("));
        findViewById(R.id.btn_tan).setOnClickListener(v -> appendFunction("tan("));
        findViewById(R.id.btn_log).setOnClickListener(v -> appendFunction("log10("));
        findViewById(R.id.btn_ln).setOnClickListener(v -> appendFunction("log("));
        findViewById(R.id.btn_sqrt).setOnClickListener(v -> appendFunction("sqrt("));
        
        findViewById(R.id.btn_fact).setOnClickListener(v -> {
            formula.append("!");
            updateDisplay();
        });

        findViewById(R.id.btn_square).setOnClickListener(v -> {
            formula.append("^2");
            updateDisplay();
        });

        findViewById(R.id.btn_inv).setOnClickListener(v -> {
            formula.append("^-1");
            updateDisplay();
        });

        findViewById(R.id.btn_ac).setOnClickListener(v -> {
            formula.setLength(0);
            resultText.setText("0");
            updateDisplay();
        });

        findViewById(R.id.btn_del).setOnClickListener(v -> handleDelete());
        findViewById(R.id.btn_del).setOnLongClickListener(v -> {
            formula.setLength(0);
            resultText.setText("0");
            updateDisplay();
            return true;
        });

        findViewById(R.id.btn_equal).setOnClickListener(v -> evaluate());
    }

    private void handleDelete() {
        if (formula.length() > 0) {
            String s = formula.toString();
            if (s.endsWith("log10(")) {
                formula.setLength(formula.length() - 6);
            } else if (s.endsWith("sqrt(")) {
                formula.setLength(formula.length() - 5);
            } else if (s.endsWith("sin(") || s.endsWith("cos(") || s.endsWith("tan(") || s.endsWith("log(")) {
                formula.setLength(formula.length() - 4);
            } else if (s.endsWith("^-1")) {
                formula.setLength(formula.length() - 3);
            } else if (s.endsWith("^2")) {
                formula.setLength(formula.length() - 2);
            } else {
                formula.deleteCharAt(formula.length() - 1);
            }
            if (formula.length() == 0) {
                resultText.setText("0");
            }
            updateDisplay();
        }
    }

    private void appendFunction(String func) {
        formula.append(func);
        updateDisplay();
    }

    private void updateDisplay() {
        formulaText.setText(formula.toString());
    }

    private void evaluate() {
        try {
            String expressionStr = formula.toString()
                    .replace("x", "*")
                    .replace("÷", "/")
                    .replace("π", "pi")
                    .replace("e", String.valueOf(Math.E));

            Operator factorial = new Operator("!", 1, true, Operator.PRECEDENCE_POWER + 1) {
                @Override
                public double apply(double... args) {
                    final int arg = (int) args[0];
                    if ((double) arg != args[0]) throw new IllegalArgumentException("Integer expected");
                    if (arg < 0) throw new IllegalArgumentException("Non-negative expected");
                    double result = 1;
                    for (int i = 1; i <= arg; i++) result *= i;
                    return result;
                }
            };

            Expression e = new ExpressionBuilder(expressionStr)
                    .operator(factorial)
                    .build();

            double result = e.evaluate();
            DecimalFormat df = new DecimalFormat("#.##########");
            resultText.setText(df.format(result));
        } catch (Exception e) {
            resultText.setText("Error");
        }
    }
}
```

### `activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/background_color"
    tools:context=".MainActivity">

    <LinearLayout
        android:id="@+id/display_layout"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:gravity="bottom|end"
        android:orientation="vertical"
        android:padding="24dp"
        app:layout_constraintBottom_toTopOf="@+id/keyboard_guideline"
        app:layout_constraintTop_toTopOf="parent">

        <TextView
            android:id="@+id/formula_text"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:ellipsize="start"
            android:singleLine="true"
            android:text=""
            android:textColor="@color/secondary_text"
            android:textSize="28sp" />

        <TextView
            android:id="@+id/result_text"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="0"
            android:textColor="@color/primary_text"
            android:textSize="56sp"
            android:textStyle="bold" />

    </LinearLayout>

    <androidx.constraintlayout.widget.Guideline
        android:id="@+id/keyboard_guideline"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        app:layout_constraintGuide_percent="0.33" />

    <GridLayout
        android:id="@+id/keyboard"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:columnCount="5"
        android:padding="4dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/keyboard_guideline">

        <!-- Row 1 -->
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_sin" style="@style/CalculatorButton" android:text="sin" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_cos" style="@style/CalculatorButton" android:text="cos" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_tan" style="@style/CalculatorButton" android:text="tan" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_log" style="@style/CalculatorButton" android:text="log" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_ln" style="@style/CalculatorButton" android:text="ln" android:layout_columnWeight="1" android:layout_rowWeight="1" />

        <!-- Row 2 -->
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_fact" style="@style/CalculatorButton" android:text="x!" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_square" style="@style/CalculatorButton" android:text="x²" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_inv" style="@style/CalculatorButton" android:text="1/x" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_sqrt" style="@style/CalculatorButton" android:text="√" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_power" style="@style/CalculatorButton" android:text="^" android:layout_columnWeight="1" android:layout_rowWeight="1" />

        <!-- Row 3 -->
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_7" style="@style/CalculatorButton" android:text="7" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_8" style="@style/CalculatorButton" android:text="8" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_9" style="@style/CalculatorButton" android:text="9" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_open_paren" style="@style/OperatorButton" android:text="(" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_close_paren" style="@style/OperatorButton" android:text=")" android:layout_columnWeight="1" android:layout_rowWeight="1" />

        <!-- Row 4 -->
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_4" style="@style/CalculatorButton" android:text="4" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_5" style="@style/CalculatorButton" android:text="5" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_6" style="@style/CalculatorButton" android:text="6" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_multiply" style="@style/OperatorButton" android:text="x" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_divide" style="@style/OperatorButton" android:text="÷" android:layout_columnWeight="1" android:layout_rowWeight="1" />

        <!-- Row 5 -->
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_1" style="@style/CalculatorButton" android:text="1" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_2" style="@style/CalculatorButton" android:text="2" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_3" style="@style/CalculatorButton" android:text="3" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_plus" style="@style/OperatorButton" android:text="+" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_minus" style="@style/OperatorButton" android:text="-" android:layout_columnWeight="1" android:layout_rowWeight="1" />

        <!-- Row 6 -->
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_0" style="@style/CalculatorButton" android:text="0" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_dot" style="@style/CalculatorButton" android:text="." android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_pi" style="@style/CalculatorButton" android:text="π" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_e" style="@style/CalculatorButton" android:text="e" android:layout_columnWeight="1" android:layout_rowWeight="1" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_del" style="@style/FunctionButton" android:text="DEL" android:layout_columnWeight="1" android:layout_rowWeight="1" />

        <!-- Row 7 -->
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_ac" style="@style/FunctionButton" android:text="AC" android:layout_columnWeight="1" android:layout_rowWeight="1" android:layout_columnSpan="3" />
        <com.google.android.material.button.MaterialButton android:id="@+id/btn_equal" style="@style/AccentButton" android:text="=" android:layout_columnWeight="1" android:layout_rowWeight="1" android:layout_columnSpan="2" />

    </GridLayout>

</androidx.constraintlayout.widget.ConstraintLayout>

```


## Output

The application displays a scientific calculator interface with buttons for numbers and scientific mathematical operations.

<img width="1902" height="1142" alt="Screenshot 2026-08-22 094650" src="https://github.com/user-attachments/assets/6d64d75c-cdbc-4359-a921-5ed29fa8bd7c" />




## Result

Thus, a **Scientific Calculator Android Application** was successfully developed and executed using Android Studio. The application accepts numerical input, performs scientific mathematical operations, and displays the calculated result successfully.
