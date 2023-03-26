package dQHrf.test.test;

import android.Manifest;
import android.app.Activity;
import android.content.Context;
import android.content.pm.PackageManager;
import android.graphics.SurfaceTexture;
import android.hardware.camera2.CameraAccessException;
import android.hardware.camera2.CameraDevice;
import android.hardware.camera2.CameraManager;
import android.hardware.camera2.CaptureRequest;
import android.hardware.camera2.params.StreamConfigurationMap;
import android.media.ImageReader;
import android.os.Handler;
import android.os.HandlerThread;
import android.support.annotation.NonNull;
import android.support.v4.app.ActivityCompat;
import android.support.v4.content.ContextCompat;
import android.util.Size;
import android.view.Surface;
import android.view.TextureView;

import com.google.appinventor.components.annotations.*;
import com.google.appinventor.components.common.ComponentCategory;
import com.google.appinventor.components.runtime.AndroidNonvisibleComponent;
import com.google.appinventor.components.runtime.ComponentContainer;
import com.google.appinventor.components.runtime.EventDispatcher;

import java.util.Arrays;

@DesignerComponent(
  version = 1,
  description = "",
  category = ComponentCategory.EXTENSION,
  nonVisible = true,
  iconName = "")

@SimpleObject(external = true)
// Libraries
@UsesLibraries(libraries = "")
// Permissions
@UsesPermissions(permissionNames = "android.permission.CAMERA")

public class CameraPreview extends AndroidNonvisibleComponent {

  // Activity and Context
  private Context context;
  private Activity activity;
  private CameraDevice cameraDevice;
  private TextureView textureView;
  private ImageReader imageReader;
  private Size previewSize;
  private HandlerThread backgroundThread;
  private Handler backgroundHandler;

  public CameraPreview(ComponentContainer container) {
    super(container.$form());
    this.activity = container.$context();
    this.context = container.$context();
    this.textureView = new TextureView(context);
    this.textureView.setSurfaceTextureListener(new TextureView.SurfaceTextureListener() {
      @Override
      public void onSurfaceTextureAvailable(SurfaceTexture surfaceTexture, int width, int height) {
        openCamera();
      }

      @Override
      public void onSurfaceTextureSizeChanged(SurfaceTexture surfaceTexture, int width, int height) {
        configureTransform(width, height);
      }

      @Override
      public boolean onSurfaceTextureDestroyed(SurfaceTexture surfaceTexture) {
        return false;
      }

      @Override
      public void onSurfaceTextureUpdated(SurfaceTexture surfaceTexture) {

      }
    });
  }

  @SimpleFunction(description = "Starts the camera preview.")
  public void StartCameraPreview() {
    if (ContextCompat.checkSelfPermission(context, Manifest.permission.CAMERA) == PackageManager.PERMISSION_GRANTED) {
      if (textureView.isAvailable()) {
        openCamera();
      } else {
        textureView.setSurfaceTextureListener(new TextureView.SurfaceTextureListener() {
          @Override
          public void onSurfaceTextureAvailable(SurfaceTexture surfaceTexture, int width, int height) {
            openCamera();
          }

          @Override
          public void onSurfaceTextureSizeChanged(SurfaceTexture surfaceTexture, int width, int height) {
            configureTransform(width, height);
          }

          @Override
          public boolean onSurfaceTextureDestroyed(SurfaceTexture surfaceTexture) {
            return false;
          }

          @Override
          public void onSurfaceTextureUpdated(SurfaceTexture surfaceTexture) {

          }
        });
      }
    } else {
      ActivityCompat.requestPermissions(activity, new String[] {
        Manifest.permission.CAMERA
      }, 0);
    }
  }

  @SimpleFunction(description = "Stops the camera preview.")
    public void StopCameraPreview() {
    if (cameraDevice != null)
  

    @Override
    public void onDestroy() {
      closeCamera();
      stopBackgroundThread();
      super.onDestroy();
    }

    private void openCamera() {
      CameraManager cameraManager = (CameraManager) context.getSystemService(Context.CAMERA_SERVICE);
      try {
        String cameraId = cameraManager.getCameraIdList()[0];
        StreamConfigurationMap map = cameraManager.getCameraCharacteristics(cameraId).get(CameraCharacteristics.SCALER_STREAM_CONFIGURATION_MAP);
        previewSize = map.getOutputSizes(SurfaceTexture.class)[0];
        configureTransform(textureView.getWidth(), textureView.getHeight());
        if (ContextCompat.checkSelfPermission(context, Manifest.permission.CAMERA) == PackageManager.PERMISSION_GRANTED) {
          cameraManager.openCamera(cameraId, new CameraDevice.StateCallback() {
            @Override
            public void onOpened(@NonNull CameraDevice camera) {
              cameraDevice = camera;
              createCameraPreviewSession();
            }

            @Override
            public void onDisconnected(@NonNull CameraDevice camera) {
              cameraDevice.close();
              cameraDevice = null;
            }

            @Override
            public void onError(@NonNull CameraDevice camera, int error) {
              cameraDevice.close();
              cameraDevice = null;
            }
          }, backgroundHandler);
        } else {
          ActivityCompat.requestPermissions(activity, new String[] {
            Manifest.permission.CAMERA
          }, 0);
        }
      } catch (CameraAccessException e) {
        e.printStackTrace();
      }
    }

    private void closeCamera() {
      if (cameraDevice != null) {
        cameraDevice.close();
        cameraDevice = null;
      }
      if (imageReader != null) {
        imageReader.close();
        imageReader = null;
      }
    }

    private void createCameraPreviewSession() {
      try {
        SurfaceTexture texture = textureView.getSurfaceTexture();
        texture.setDefaultBufferSize(previewSize.getWidth(), previewSize.getHeight());
        Surface surface = new Surface(texture);
        CaptureRequest.Builder captureRequestBuilder = cameraDevice.createCaptureRequest(CameraDevice.TEMPLATE_PREVIEW);
        captureRequestBuilder.addTarget(surface);
        cameraDevice.createCaptureSession(Arrays.asList(surface), new CameraCaptureSession.StateCallback() {
          @Override
          public void onConfigured(@NonNull CameraCaptureSession session) {
            try {
              session.setRepeatingRequest(captureRequestBuilder.build(), null, backgroundHandler);
            } catch (CameraAccessException e) {
              e.printStackTrace();
            }
          }

          @Override
          public void onConfigureFailed(@NonNull CameraCaptureSession session) {

            }
            }, null);
            } catch (CameraAccessException e) {
            e.printStackTrace();
            }
            }

    private void configureTransform(int viewWidth, int viewHeight) {
      if (null == textureView || null == previewSize) {
        return;
      }
      int rotation = activity.getWindowManager().getDefaultDisplay().getRotation();
      Matrix matrix = new Matrix();
      RectF viewRectF = new RectF(0, 0, viewWidth, viewHeight);
      RectF bufferRectF = new RectF(0, 0, previewSize.getHeight(), previewSize.getWidth());
      float centerX = viewRectF.centerX();
      float centerY = viewRectF.centerY();
      if (Surface.ROTATION_90 == rotation || Surface.ROTATION_270 == rotation) {
        bufferRectF.offset(centerX - bufferRectF.centerX(), centerY - bufferRectF.centerY());
        matrix.setRectToRect(viewRectF, bufferRectF, Matrix.ScaleToFit.FILL);
        float scale = Math.max((float) viewHeight / previewSize.getHeight(), (float) viewWidth / previewSize.getWidth());
        matrix.postScale(scale, scale, centerX, centerY);
        matrix.postRotate(90 * (rotation - 2), centerX, centerY);
      } else if (Surface.ROTATION_180 == rotation) {
        matrix.postRotate(180, centerX, centerY);
      }
      textureView.setTransform(matrix);
    }
    private void startBackgroundThread() {
      backgroundThread = new HandlerThread("Camera Background");
      backgroundThread.start();
      backgroundHandler = new Handler(backgroundThread.getLooper());
    }

    private void stopBackgroundThread() {
      backgroundThread.quitSafely();
      try {
        backgroundThread.join();
        backgroundThread = null;
        backgroundHandler = null;
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }

    @SimpleFunction(description = "Starts the camera preview")
    public void StartPreview() {
      textureView = new TextureView(context);
      activity.setContentView(textureView);
      textureView.setSurfaceTextureListener(textureListener);
    }

    private TextureView.SurfaceTextureListener textureListener = new TextureView.SurfaceTextureListener() {
      @Override
      public void onSurfaceTextureAvailable(@NonNull SurfaceTexture surface, int width, int height) {
        openCamera();
      }

      @Override
      public void onSurfaceTextureSizeChanged(@NonNull SurfaceTexture surface, int width, int height) {
        configureTransform(width, height);
      }

      @Override
      public boolean onSurfaceTextureDestroyed(@NonNull SurfaceTexture surface) {
        return false;
      }

      @Override
      public void onSurfaceTextureUpdated(@NonNull SurfaceTexture surface) {

      }
    };
  }
