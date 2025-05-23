# Alert-Msg
AndroidManifest.xml:
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
   xmlns:tools="http://schemas.android.com/tools">
   <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
   <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
   <application
       android:allowBackup="true"
       android:icon="@mipmap/ic_launcher"
       android:label="@string/app_name"
       android:theme="@style/Theme.AppCompat.DayNight.NoActionBar">
       <activity android:name=".MainActivity"
           android:label="@string/app_name"
           android:exported="true">
           <intent-filter>
               <action android:name="android.intent.action.MAIN" />
               <category android:name="android.intent.category.LAUNCHER" />
           </intent-filter>
       </activity>
   </application>


</manifest>
 
MainActivity.java:
package com.example.location;

import android.Manifest;
import android.content.pm.PackageManager;
import android.location.Criteria;
import android.location.Location;
import android.location.LocationListener;
import android.location.LocationManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

public class MainActivity extends AppCompatActivity {

   private LocationManager locationManager;

   @Override
   protected void onCreate(Bundle savedInstanceState) {
       super.onCreate(savedInstanceState);
       setContentView(R.layout.activity_main);

       locationManager = (LocationManager) getSystemService(LOCATION_SERVICE);

       Button getLocationButton = findViewById(R.id.get_location_button);
       getLocationButton.setOnClickListener(v -> fetchLocation());
   }

   private void fetchLocation() {
       // Check if permissions are granted
       if (ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED
               && ActivityCompat.checkSelfPermission(this, Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED) {
           // Request permissions if not granted
           ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.ACCESS_FINE_LOCATION, Manifest.permission.ACCESS_COARSE_LOCATION}, 1);
           return;
       }

       // Create a LocationListener to get location updates
       LocationListener locationListener = new LocationListener() {
           @Override
           public void onLocationChanged(Location location) {
               // Location updated, get latitude and longitude
               if (location != null) {
                   double latitude = location.getLatitude();
                   double longitude = location.getLongitude();
                   Toast.makeText(MainActivity.this, "Latitude: " + latitude + "\nLongitude: " + longitude, Toast.LENGTH_LONG).show();
               }
           }

           @Override
           public void onStatusChanged(String provider, int status, Bundle extras) {}

           @Override
           public void onProviderEnabled(String provider) {}

           @Override
           public void onProviderDisabled(String provider) {}
       };

       // Use the best provider for location updates (GPS or Network)
       Criteria criteria = new Criteria();
       String bestProvider = locationManager.getBestProvider(criteria, true);

       // Request location updates from the provider
       try {
           locationManager.requestLocationUpdates(bestProvider, 1000, 1, locationListener); // 1000 ms, 1 meter
       } catch (SecurityException e) {
           e.printStackTrace();
           Toast.makeText(this, "Error fetching location.", Toast.LENGTH_SHORT).show();
       }
   }

   // Handling permission request result
   @Override
   public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
       super.onRequestPermissionsResult(requestCode, permissions, grantResults);

       if (requestCode == 1) {
           if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
               // Permission granted, fetch location
               fetchLocation();
           } else {
               Toast.makeText(this, "Permission denied. Cannot fetch location.", Toast.LENGTH_SHORT).show();
           }
       }
   }
}
 
activity_main.xml:
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:layout_width="match_parent"
   android:layout_height="match_parent"
   android:orientation="vertical"
   android:gravity="center"
   android:padding="16dp">

   <Button
       android:id="@+id/get_location_button"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:text="Get Current Location"
       android:textSize="18sp" />

</LinearLayout>
 
