# SlotMachineZ
package com.example.slotmachinez;

        import androidx.appcompat.app.AppCompatActivity;

        import android.os.Bundle;
        import android.view.View;
        import android.widget.Button;
        import android.widget.ImageView;
        import android.widget.TextView;

        import java.util.Random;

public class MainActivity extends AppCompatActivity {
    private TextView txtFine;
    private ImageView imgI1, imgI2, imgI3;
    private Wheel w1, w2, w3;
    private Button btnClick;
    private boolean isStarted;

    public static final Random RANDOM = new Random();
    public static long randomLong(long lower, long upper) {
        return lower + (long) (RANDOM.nextDouble() * (upper - lower));
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        imgI1 = (ImageView) findViewById(R.id.imgI1);
        imgI2 = (ImageView) findViewById(R.id.imgI2);
        imgI3 = (ImageView) findViewById(R.id.imgI3);
        btnClick = (Button) findViewById(R.id.btnClick);
        txtFine = (TextView) findViewById(R.id.txtFine);

        btnClick.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (isStarted) {
                    w1.stopWheel();
                    w2.stopWheel();
                    w3.stopWheel();

                    if (w1.currentIndex == w2.currentIndex && w2.currentIndex == w3.currentIndex) {
                        txtFine.setText("Hai vinto il grande premio!!!");
                    } else if (w1.currentIndex == w2.currentIndex || w2.currentIndex == w3.currentIndex || w1.currentIndex == w3.currentIndex) {
                        txtFine.setText("Hai vinto, ma non tanto!");
                    } else {
                        txtFine.setText("Hai perso...");
                    }

                    btnClick.setText("Rigioca!");
                    isStarted = false;

                } else {

                    w1 = new Wheel(new Wheel.WheelListener() {
                        @Override
                        public void newImage(final int img) {
                            runOnUiThread(new Runnable() {
                                @Override
                                public void run() {
                                    imgI1.setImageResource(img);
                                }
                            });
                        }
                    }, 200, randomLong(0, 200));

                    w1.start();

                    w2 = new Wheel(new Wheel.WheelListener() {
                        @Override
                        public void newImage(final int img) {
                            runOnUiThread(new Runnable() {
                                @Override
                                public void run() {
                                    imgI2.setImageResource(img);
                                }
                            });
                        }
                    }, 200, randomLong(150, 400));

                    w2.start();

                    w3 = new Wheel(new Wheel.WheelListener() {
                        @Override
                        public void newImage(final int img) {
                            runOnUiThread(new Runnable() {
                                @Override
                                public void run() {
                                    imgI3.setImageResource(img);
                                }
                            });
                        }
                    }, 200, randomLong(150, 400));

                    w3.start();

                    btnClick.setText("Ferma!");
                    txtFine.setText("");
                    isStarted = true;
                }
            }
        });
    }
}





package com.example.slotmachinez;

public class Wheel extends Thread {

    interface WheelListener {
        void newImage(int img);
    }

    private static int[] imgs = {R.drawable.slot1, R.drawable.slot2, R.drawable.slot3, R.drawable.slot4,
            R.drawable.slot5, R.drawable.slot6, R.drawable.slot7, R.drawable.slot8, R.drawable.slot9};
    public int currentIndex;
    private WheelListener wheelListener;
    private long frameDuration;
    private long startIn;
    private boolean isStarted;

    public Wheel(WheelListener wheelListener, long frameDuration, long startIn) {
        this.wheelListener = wheelListener;
        this.frameDuration = frameDuration;
        this.startIn = startIn;
        currentIndex = 0;
        isStarted = true;
    }

    public void nextImg() {
        currentIndex++;

        if (currentIndex == imgs.length) {
            currentIndex = 0;
        }
    }

    @Override
    public void run() {
        try {
            Thread.sleep(startIn);
        } catch (InterruptedException e) {
        }

        while(isStarted) {
            try {
                Thread.sleep(frameDuration);
            } catch (InterruptedException e) {
            }

            nextImg();

            if (wheelListener != null) {
                wheelListener.newImage(imgs[currentIndex]);
            }
        }
    }

    public void stopWheel() {
        isStarted = false;
    }
}





<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
xmlns:app="http://schemas.android.com/apk/res-auto"
xmlns:tools="http://schemas.android.com/tools"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:background="#E47474"
tools:context=".MainActivity">

<ImageView
    android:id="@+id/imgI1"
    android:layout_width="120dp"
    android:layout_height="120dp"
    android:layout_marginStart="16dp"
    android:layout_marginTop="80dp"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    app:srcCompat="@drawable/money" />

<ImageView
    android:id="@+id/imgI2"
    android:layout_width="120dp"
    android:layout_height="120dp"
    android:layout_marginTop="80dp"
    app:layout_constraintEnd_toStartOf="@+id/imgI3"
    app:layout_constraintHorizontal_bias="0.526"
    app:layout_constraintStart_toEndOf="@+id/imgI1"
    app:layout_constraintTop_toTopOf="parent"
    app:srcCompat="@drawable/money" />

<ImageView
    android:id="@+id/imgI3"
    android:layout_width="120dp"
    android:layout_height="120dp"
    android:layout_marginTop="80dp"
    android:layout_marginEnd="16dp"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    app:srcCompat="@drawable/money" />

<Button
    android:id="@+id/btnClick"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_marginStart="120dp"
    android:layout_marginEnd="120dp"
    android:layout_marginBottom="99dp"
    android:backgroundTint="#009688"
    android:text="Clicca qui!!!"
    android:textAlignment="center"
    android:textStyle="bold|italic"
    app:layout_constraintBottom_toTopOf="@+id/txtFine"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent" />

<TextView
    android:id="@+id/txtFine"
    android:layout_width="0dp"
    android:layout_height="30dp"
    android:layout_marginStart="100dp"
    android:layout_marginEnd="100dp"
    android:layout_marginBottom="228dp"
    android:textAlignment="center"
    android:textSize="16sp"
    android:textStyle="bold|italic"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintHorizontal_bias="0.0"
    app:layout_constraintStart_toStartOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
