编写一个简单的安卓手电筒应用涉及多个步骤，包括获取摄像头权限、打开和关闭闪光灯等。以下是一个完整的示例应用程序的步骤和代码。

### 1. 设置权限

首先，在 `AndroidManifest.xml` 文件中声明访问摄像头的权限：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.flashlight">

    <uses-permission android:name="android.permission.CAMERA" />
    <uses-feature android:name="android.hardware.camera.flash" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Flashlight">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```

### 2. 创建布局文件

在 `res/layout` 文件夹中创建一个简单的布局文件 `activity_main.xml`：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    android:gravity="center"
    android:background="#000000">

    <Button
        android:id="@+id/button_toggle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Turn On"
        android:textSize="24sp"
        android:padding="16dp"
        android:background="@drawable/button_background"
        android:textColor="#FFFFFF" />

</RelativeLayout>
```

### 3. 设置按钮背景

在 `res/drawable` 文件夹中创建一个 `button_background.xml` 文件来定义按钮的背景：

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true">
        <shape android:shape="rectangle">
            <solid android:color="#FF0000" />
            <corners android:radius="8dp" />
        </shape>
    </item>
    <item>
        <shape android:shape="rectangle">
            <solid android:color="#00FF00" />
            <corners android:radius="8dp" />
        </shape>
    </item>
</selector>
```

### 4. 编写MainActivity

创建 `MainActivity` 类，并编写打开和关闭手电筒的逻辑：

```java
package com.example.flashlight;

import android.Manifest;
import android.content.pm.PackageManager;
import android.hardware.camera2.CameraAccessException;
import android.hardware.camera2.CameraManager;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;
import androidx.core.content.ContextCompat;

public class MainActivity extends AppCompatActivity {

    private static final int CAMERA_REQUEST = 50;
    private boolean isFlashOn = false;
    private CameraManager cameraManager;
    private String cameraId;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        final Button buttonToggle = findViewById(R.id.button_toggle);

        // Check if the device has a camera flash
        boolean isFlashAvailable = getPackageManager()
                .hasSystemFeature(PackageManager.FEATURE_CAMERA_FLASH);

        if (!isFlashAvailable) {
            Toast.makeText(this, "No flash available on your device", Toast.LENGTH_SHORT).show();
            return;
        }

        // Request camera permission if not granted
        boolean hasCameraPermission = ContextCompat.checkSelfPermission(this, Manifest.permission.CAMERA)
                == PackageManager.PERMISSION_GRANTED;
        if (!hasCameraPermission) {
            ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.CAMERA}, CAMERA_REQUEST);
        }

        cameraManager = (CameraManager) getSystemService(CAMERA_SERVICE);
        try {
            cameraId = cameraManager.getCameraIdList()[0];
        } catch (CameraAccessException e) {
            e.printStackTrace();
        }

        buttonToggle.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (isFlashOn) {
                    turnOffFlash();
                    buttonToggle.setText("Turn On");
                } else {
                    turnOnFlash();
                    buttonToggle.setText("Turn Off");
                }
            }
        });
    }

    private void turnOnFlash() {
        try {
            if (cameraManager != null) {
                cameraManager.setTorchMode(cameraId, true);
                isFlashOn = true;
            }
        } catch (CameraAccessException e) {
            e.printStackTrace();
        }
    }

    private void turnOffFlash() {
        try {
            if (cameraManager != null) {
                cameraManager.setTorchMode(cameraId, false);
                isFlashOn = false;
            }
        } catch (CameraAccessException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        if (requestCode == CAMERA_REQUEST) {
            if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                Toast.makeText(this, "Camera permission granted", Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(this, "Camera permission denied", Toast.LENGTH_SHORT).show();
            }
        }
    }
}
```

### 5. 运行应用

通过上述步骤，你已经创建了一个简单的手电筒应用。启动应用后，按下按钮即可打开和关闭闪光灯。

### 总结

这是一款基本的手电筒应用，它展示了如何请求权限、访问摄像头并控制闪光灯。实际项目中，你可能需要处理更多的细节和异常情况。
在Android应用中，你可以通过创建一个计算密集型的任务并在单独的线程上运行来实现CPU满载的效果。然而，请注意，这种做法可能会导致设备过热、耗电快以及其他不良后果。在实际应用中，请谨慎使用。

以下是添加一个按钮来实现CPU满载的完整示例。

### 1. 修改布局文件

在 `res/layout/activity_main.xml` 中添加一个新的按钮：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    android:gravity="center"
    android:background="#000000">

    <Button
        android:id="@+id/button_toggle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Turn On"
        android:textSize="24sp"
        android:padding="16dp"
        android:background="@drawable/button_background"
        android:textColor="#FFFFFF"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="20dp" />

    <Button
        android:id="@+id/button_cpu_load"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Load CPU"
        android:textSize="24sp"
        android:padding="16dp"
        android:background="@drawable/button_background"
        android:textColor="#FFFFFF"
        android:layout_below="@id/button_toggle"
        android:layout_centerHorizontal="true" />

</RelativeLayout>
```

### 2. 修改 `MainActivity`

在 `MainActivity` 中实现新按钮的点击事件，启动一个计算密集型任务来满载CPU：

```java
package com.example.flashlight;

import android.Manifest;
import android.content.pm.PackageManager;
import android.hardware.camera2.CameraAccessException;
import android.hardware.camera2.CameraManager;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;
import androidx.core.content.ContextCompat;

public class MainActivity extends AppCompatActivity {

    private static final int CAMERA_REQUEST = 50;
    private boolean isFlashOn = false;
    private CameraManager cameraManager;
    private String cameraId;
    private boolean isCpuLoading = false;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        final Button buttonToggle = findViewById(R.id.button_toggle);
        final Button buttonCpuLoad = findViewById(R.id.button_cpu_load);

        // Check if the device has a camera flash
        boolean isFlashAvailable = getPackageManager()
                .hasSystemFeature(PackageManager.FEATURE_CAMERA_FLASH);

        if (!isFlashAvailable) {
            Toast.makeText(this, "No flash available on your device", Toast.LENGTH_SHORT).show();
            return;
        }

        // Request camera permission if not granted
        boolean hasCameraPermission = ContextCompat.checkSelfPermission(this, Manifest.permission.CAMERA)
                == PackageManager.PERMISSION_GRANTED;
        if (!hasCameraPermission) {
            ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.CAMERA}, CAMERA_REQUEST);
        }

        cameraManager = (CameraManager) getSystemService(CAMERA_SERVICE);
        try {
            cameraId = cameraManager.getCameraIdList()[0];
        } catch (CameraAccessException e) {
            e.printStackTrace();
        }

        buttonToggle.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (isFlashOn) {
                    turnOffFlash();
                    buttonToggle.setText("Turn On");
                } else {
                    turnOnFlash();
                    buttonToggle.setText("Turn Off");
                }
            }
        });

        buttonCpuLoad.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (!isCpuLoading) {
                    startCpuLoad();
                    buttonCpuLoad.setText("Stop CPU Load");
                } else {
                    stopCpuLoad();
                    buttonCpuLoad.setText("Load CPU");
                }
            }
        });
    }

    private void turnOnFlash() {
        try {
            if (cameraManager != null) {
                cameraManager.setTorchMode(cameraId, true);
                isFlashOn = true;
            }
        } catch (CameraAccessException e) {
            e.printStackTrace();
        }
    }

    private void turnOffFlash() {
        try {
            if (cameraManager != null) {
                cameraManager.setTorchMode(cameraId, false);
                isFlashOn = false;
            }
        } catch (CameraAccessException e) {
            e.printStackTrace();
        }
    }

    private void startCpuLoad() {
        isCpuLoading = true;
        new Thread(new Runnable() {
            @Override
            public void run() {
                while (isCpuLoading) {
                    // Busy-wait loop to load the CPU
                }
            }
        }).start();
    }

    private void stopCpuLoad() {
        isCpuLoading = false;
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        if (requestCode == CAMERA_REQUEST) {
            if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                Toast.makeText(this, "Camera permission granted", Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(this, "Camera permission denied", Toast.LENGTH_SHORT).show();
            }
        }
    }
}
```

### 3. 运行应用

通过上述步骤，你已经在手电筒应用中添加了一个按钮，用于让CPU满载。启动应用后，按下“Load CPU”按钮，即可使CPU满载，再次按下按钮可以停止CPU满载。

### 总结

这个示例展示了如何在Android应用中通过按钮触发CPU满载任务。请注意，这种操作可能会对设备性能和电池寿命产生不良影响，因此在实际应用中应谨慎使用。