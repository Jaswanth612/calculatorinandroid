# Ex.No:5 Develop a simple calculator using android studio.

## AIM:

To develop a program to develop a simple calculator in Android Studio.

## EQUIPMENTS REQUIRED:

Android Studio(Min.required Artic Fox)

## ALGORITHM:

Step 1: Open Android Stdio and then click on File -> New -> New project.

Step 2: Then type the Application name as calculator and click Next. 

Step 3: Then select the Minimum SDK as shown below and click Next.

Step 4: Then select the Empty Activity and click Next. Finally click Finish.

Step 5: Design layout using UI components in activity_main.xml.

Step 6: Display the calculator operation in MainActivity file.

Step 7: Save and run the application.

## PROGRAM:
```
/*
Program to print the text “calculator operation”.
Developed by: Jaswanth S
Registration Number : 212223220037
*/
```

#### MainActivity.java
```
package com.example.calculator;

import android.os.Bundle;
import android.view.View;
import android.widget.ArrayAdapter;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

import com.google.android.material.bottomsheet.BottomSheetDialog;
import com.google.android.material.button.MaterialButton;

import java.text.DecimalFormat;
import java.util.ArrayList;
import java.util.List;

public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    private TextView tvExpression;
    private TextView tvResult;

    private String expression = "";

    private boolean scientificMode = false;

    private boolean resultDisplayed = false;

    private double memory = 0;

    private final List<String> history = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tvExpression = findViewById(R.id.tvExpression);
        tvResult = findViewById(R.id.tvResult);

        initButtons();
    }

    private void initButtons() {

        int[] ids = {

                R.id.btn0,R.id.btn1,R.id.btn2,R.id.btn3,R.id.btn4,
                R.id.btn5,R.id.btn6,R.id.btn7,R.id.btn8,R.id.btn9,

                R.id.btnDot,

                R.id.btnAdd,
                R.id.btnMinus,
                R.id.btnMultiply,
                R.id.btnDivide,
                R.id.btnMod,

                R.id.btnEqual,
                R.id.btnAC,
                R.id.btnC,

                R.id.btnMC,
                R.id.btnMR,
                R.id.btnMPlus,
                R.id.btnMMinus,

                R.id.btnToggleMode,
                R.id.btnHistory,



        };

        for(int id:ids){

            View v=findViewById(id);

            if(v!=null)
                v.setOnClickListener(this);

        }

    }

    @Override
    public void onClick(View view) {

        int id=view.getId();

        if(id==R.id.btnAC){

            expression="";
            tvExpression.setText("");
            tvResult.setText("0");
            return;
        }

        if(id==R.id.btnC){

            if(expression.length()>0){

                expression=expression.substring(0,expression.length()-1);
                tvExpression.setText(expression);

            }

            return;
        }

        if(id==R.id.btnEqual){

            calculate();

            return;
        }

        if(id==R.id.btnHistory){

            showHistory();

            return;
        }



        if(id==R.id.btnMC){

            memory=0;
            Toast.makeText(this,"Memory Cleared",Toast.LENGTH_SHORT).show();
            return;
        }

        if(id==R.id.btnMR){

            append(format(memory));

            return;
        }

        if(id==R.id.btnMPlus){

            memory+=getCurrentResult();
            Toast.makeText(this,"Added To Memory",Toast.LENGTH_SHORT).show();
            return;
        }

        if(id==R.id.btnMMinus){

            memory-=getCurrentResult();
            Toast.makeText(this,"Subtracted From Memory",Toast.LENGTH_SHORT).show();
            return;
        }

        MaterialButton button=(MaterialButton)view;

        String text=button.getText().toString();

        if(resultDisplayed){

            expression="";
            resultDisplayed=false;

        }

        append(text);

    }

    private void append(String text){

        expression+=text;

        tvExpression.setText(expression);

    }

    private void calculate(){

        if(expression.isEmpty())
            return;

        try{

            String exp=expression;

            exp=exp.replace("×","*");
            exp=exp.replace("÷","/");
            exp=exp.replace("−","-");

            double result=eval(exp);

            String ans=format(result);

            tvResult.setText(ans);

            history.add(0,expression+" = "+ans);

            resultDisplayed=true;

        }

        catch(Exception e){

            tvResult.setText("Error");

        }

    }

    private void toggleScientific(){

        scientificMode=!scientificMode;

        int visibility=scientificMode?View.VISIBLE:View.GONE;

        int[] sci={



        };

        for(int id:sci){

            View v=findViewById(id);

            if(v!=null)
                v.setVisibility(visibility);

        }

    }

    private void showHistory(){

        BottomSheetDialog dialog=new BottomSheetDialog(this);

        ListView listView=new ListView(this);

        ArrayAdapter<String> adapter=new ArrayAdapter<>(this,
                android.R.layout.simple_list_item_1,
                history);

        listView.setAdapter(adapter);

        dialog.setContentView(listView);

        dialog.show();

    }

    private double getCurrentResult(){

        try{

            return Double.parseDouble(tvResult.getText().toString());

        }

        catch(Exception e){

            return 0;

        }

    }

    private String format(double d){

        DecimalFormat df=new DecimalFormat("#.########");

        return df.format(d);

    }

    /* ---------------- Expression Parser ---------------- */

    public static double eval(final String str){

        return new Object(){

            int pos=-1,ch;

            void nextChar(){

                ch=(++pos<str.length())?str.charAt(pos):-1;

            }

            boolean eat(int charToEat){

                while(ch==' ') nextChar();

                if(ch==charToEat){

                    nextChar();

                    return true;

                }

                return false;

            }

            double parse(){

                nextChar();

                double x=parseExpression();

                return x;

            }

            double parseExpression(){

                double x=parseTerm();

                while(true){

                    if(eat('+')) x+=parseTerm();

                    else if(eat('-')) x-=parseTerm();

                    else return x;

                }

            }

            double parseTerm(){

                double x=parseFactor();

                while(true){

                    if(eat('*')) x*=parseFactor();

                    else if(eat('/')) x/=parseFactor();

                    else if(eat('%')) x%=parseFactor();

                    else return x;

                }

            }

            double parseFactor(){

                if(eat('+')) return parseFactor();

                if(eat('-')) return -parseFactor();

                double x;

                int startPos=this.pos;

                if(eat('(')){

                    x=parseExpression();

                    eat(')');

                }

                else if((ch>='0'&&ch<='9')||ch=='.'){

                    while((ch>='0'&&ch<='9')||ch=='.')
                        nextChar();

                    x=Double.parseDouble(str.substring(startPos,this.pos));

                }

                else{

                    throw new RuntimeException("Unexpected");

                }

                return x;

            }

        }.parse();

    }

}
```

#### activity_main.xml
```
<?xml version="1.0" encoding="utf-8"?>

<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"

    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"

    android:background="#F5F5F5"

    tools:context=".MainActivity">


    <!-- Display Card -->

    <com.google.android.material.card.MaterialCardView

        android:id="@+id/displayCard"

        android:layout_width="0dp"
        android:layout_height="220dp"

        android:layout_margin="20dp"

        app:cardCornerRadius="24dp"
        app:cardElevation="8dp"

        app:cardBackgroundColor="#FFFFFF"

        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent">

        <LinearLayout

            android:layout_width="match_parent"
            android:layout_height="match_parent"

            android:gravity="bottom|end"

            android:orientation="vertical"

            android:padding="24dp">

            <TextView

                android:id="@+id/tvExpression"

                android:layout_width="wrap_content"
                android:layout_height="wrap_content"

                android:text=""

                android:textColor="#777777"

                android:textSize="22sp"/>

            <TextView

                android:id="@+id/tvResult"

                android:layout_width="wrap_content"
                android:layout_height="wrap_content"

                android:text="0"

                android:textStyle="bold"

                android:textColor="#222222"

                android:textSize="52sp"/>

        </LinearLayout>

    </com.google.android.material.card.MaterialCardView>


    <!-- History + Scientific -->

    <LinearLayout

        android:id="@+id/topControls"

        android:layout_width="0dp"
        android:layout_height="wrap_content"

        android:layout_marginStart="20dp"
        android:layout_marginEnd="20dp"

        android:orientation="horizontal"

        android:gravity="center_vertical"

        app:layout_constraintTop_toBottomOf="@id/displayCard"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent">

        <com.google.android.material.button.MaterialButton

            android:id="@+id/btnHistory"

            style="@style/Widget.Material3.Button.TextButton"

            android:layout_width="wrap_content"
            android:layout_height="wrap_content"

            android:text="History"

            android:textColor="#777777"/>


        <View
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_weight="1"/>


        <com.google.android.material.button.MaterialButton

            android:id="@+id/btnToggleMode"

            style="@style/Widget.Material3.Button.TextButton"

            android:layout_width="wrap_content"
            android:layout_height="wrap_content"

            android:text="Scientific"

            android:textColor="#FF9800"/>

    </LinearLayout>



    <!-- Memory Row -->

    <GridLayout

        android:id="@+id/memoryRow"

        android:layout_width="0dp"
        android:layout_height="wrap_content"

        android:layout_margin="10dp"

        android:columnCount="4"

        app:layout_constraintTop_toBottomOf="@id/topControls"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent">


        <com.google.android.material.button.MaterialButton

            android:id="@+id/btnMC"

            style="@style/Widget.Material3.Button.TextButton"

            android:layout_width="0dp"
            android:layout_height="50dp"

            android:layout_columnWeight="1"

            android:text="MC"

            android:textColor="#666666"/>


        <com.google.android.material.button.MaterialButton

            android:id="@+id/btnMR"

            style="@style/Widget.Material3.Button.TextButton"

            android:layout_width="0dp"
            android:layout_height="50dp"

            android:layout_columnWeight="1"

            android:text="MR"

            android:textColor="#666666"/>


        <com.google.android.material.button.MaterialButton

            android:id="@+id/btnMPlus"

            style="@style/Widget.Material3.Button.TextButton"

            android:layout_width="0dp"
            android:layout_height="50dp"

            android:layout_columnWeight="1"

            android:text="M+"

            android:textColor="#666666"/>


        <com.google.android.material.button.MaterialButton

            android:id="@+id/btnMMinus"

            style="@style/Widget.Material3.Button.TextButton"

            android:layout_width="0dp"
            android:layout_height="50dp"

            android:layout_columnWeight="1"

            android:text="M-"

            android:textColor="#666666"/>

    </GridLayout>


    <!-- Keypad Starts Here -->
    <GridLayout

        android:id="@+id/keypad"

        android:layout_width="0dp"
        android:layout_height="0dp"

        android:layout_margin="12dp"

        android:columnCount="4"

        app:layout_constraintTop_toBottomOf="@id/memoryRow"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent">

        <!-- Row 1 -->

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnAC"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="AC"
            android:textColor="#FF5252"
            app:backgroundTint="#FFEAEA"
            app:cornerRadius="18dp"/>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnC"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="C"
            android:textColor="#666666"
            app:backgroundTint="#EEEEEE"
            app:cornerRadius="18dp"/>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnMod"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="%"
            android:textColor="#666666"
            app:backgroundTint="#EEEEEE"
            app:cornerRadius="18dp"/>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnDivide"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="÷"
            android:textSize="24sp"
            android:textColor="#FF9800"
            app:backgroundTint="#FFF3E0"
            app:cornerRadius="18dp"/>


        <!-- Row 2 -->

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn7"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="7"
            android:textSize="22sp"
            android:textColor="#222222"
            app:backgroundTint="#FFFFFF"
            app:cornerRadius="18dp"/>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn8"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="8"
            android:textSize="22sp"
            android:textColor="#222222"
            app:backgroundTint="#FFFFFF"
            app:cornerRadius="18dp"/>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn9"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="9"
            android:textSize="22sp"
            android:textColor="#222222"
            app:backgroundTint="#FFFFFF"
            app:cornerRadius="18dp"/>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnMultiply"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="×"
            android:textSize="24sp"
            android:textColor="#FF9800"
            app:backgroundTint="#FFF3E0"
            app:cornerRadius="18dp"/>


        <!-- Row 3 -->

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn4"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="4"
            android:textSize="22sp"
            android:textColor="#222222"
            app:backgroundTint="#FFFFFF"
            app:cornerRadius="18dp"/>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn5"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="5"
            android:textSize="22sp"
            android:textColor="#222222"
            app:backgroundTint="#FFFFFF"
            app:cornerRadius="18dp"/>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn6"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="6"
            android:textSize="22sp"
            android:textColor="#222222"
            app:backgroundTint="#FFFFFF"
            app:cornerRadius="18dp"/>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnMinus"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="−"
            android:textSize="24sp"
            android:textColor="#FF9800"
            app:backgroundTint="#FFF3E0"
            app:cornerRadius="18dp"/>
        <!-- Row 4 -->

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn1"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="1"
            android:textSize="22sp"
            android:textColor="#222222"
            app:backgroundTint="#FFFFFF"
            app:cornerRadius="18dp"/>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn2"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="2"
            android:textSize="22sp"
            android:textColor="#222222"
            app:backgroundTint="#FFFFFF"
            app:cornerRadius="18dp"/>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn3"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="3"
            android:textSize="22sp"
            android:textColor="#222222"
            app:backgroundTint="#FFFFFF"
            app:cornerRadius="18dp"/>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnAdd"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="+"
            android:textSize="24sp"
            android:textColor="#FF9800"
            app:backgroundTint="#FFF3E0"
            app:cornerRadius="18dp"/>


        <!-- Row 5 -->

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btn0"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="0"
            android:textSize="22sp"
            android:textColor="#222222"
            app:backgroundTint="#FFFFFF"
            app:cornerRadius="18dp"/>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnDot"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnWeight="1"
            android:layout_margin="6dp"
            android:text="."
            android:textSize="22sp"
            android:textColor="#222222"
            app:backgroundTint="#FFFFFF"
            app:cornerRadius="18dp"/>

        <com.google.android.material.button.MaterialButton
            android:id="@+id/btnEqual"
            android:layout_width="0dp"
            android:layout_height="70dp"
            android:layout_columnSpan="2"
            android:layout_columnWeight="2"
            android:layout_margin="6dp"
            android:text="="
            android:textSize="24sp"
            android:textStyle="bold"
            android:textColor="#FFFFFF"
            app:backgroundTint="#2ECC71"
            app:cornerRadius="18dp"/>

    </GridLayout>

</androidx.constraintlayout.widget.ConstraintLayout>


```

## OUTPUT
<img width="1908" height="1075" alt="image" src="https://github.com/user-attachments/assets/1880cd2b-089a-4b9f-add4-60a5d77ee907" />


## RESULT
Thus a Simple Android Application develop a program to create simple calculator in Android Studio is developed and executed successfully.

