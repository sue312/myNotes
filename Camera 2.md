<h1><center>Camera 2</center></h1>

# 一、Camera2中比较重要的类及方法

## 1. CameraManager

```
CameraManager mCameraManager = (CameraManager)getSystemService(Context.CAMERA_SERVICE);
```

> 一个 用于检测、表征和连接到 `CameraDevices`的系统服务管理器. 通过`getCameraIdList`选择要打开的摄像头，通过`getCameraCharacteristics`得到固定不变的相机能力，然后调用`openCamera`打开摄像头，在`CameraDevice.StateCallback#onOpened`得到相机实例`CameraDevice`，然后调用`CameraDevice#createCaptureSession`和 `CameraDevice#createCaptureRequest`设置预览捕获等

摄像头管理器，用于打开和关闭系统摄像头

- **getCameraIdList()** ：
  返回当前设备中可用的相机列表（id值为从0开始的Int值）
- **getCameraCharacteristics(String cameraId)** ：
  根据摄像头id返回该摄像头的相关信息
- **openCamera(String cameraId, final CameraDevice.StateCallback callback,Handler handler)**：
  打开指定cameraId的相机。参数callback为相机打开时的回调，参数handler为callback被调用时所在的线程

**获取相机 ID 列表**

>```java
>//方法
>public String[] getCameraIdList ()
>//使用
>mCameraManager.getCameraIdList();
>//遍历
>for (String cameraId : mCameraManager.getCameraIdList()) 
>```
>
>```
>CAMERA_FACING_BACK = 0	后摄
>CAMERA_FACING_FRONT = 1	前摄
>```

**查询摄像头设备的能力**

>```java
>//方法
>public CameraCharacteristics getCameraCharacteristics (String cameraId)
>//使用（可获取指定摄像头的相关特性）
>CameraCharacteristics characteristics = >mCameraManager.getCameraCharacteristics(cameraId);
>```
>
>查询摄像头设备的摄像头扩展能力
>
>```java
>public CameraExtensionCharacteristics getCameraExtensionCharacteristics (String cameraId)
>```

**打开与具有给定 ID 的相机的连接**

>```java
>public void openCamera (String cameraId, CameraDevice.StateCallback >callback, Handler handler)
>```

## 2. CameraDevice

描述系统摄像头，类似于早期的Camera

- **createCaptureRequest(int templateType)**：
  创建一个新的Capture请求。参数templateType代表了请求类型，请求类型一共分为六种，分别为：

> 1. TEMPLATE_PREVIEW : 创建预览的请求
> 2. TEMPLATE_STILL_CAPTURE: 创建一个适合于静态图像捕获的请求，图像质量优先于帧速率
> 3. TEMPLATE_RECORD : 创建视频录制的请求
> 4. TEMPLATE_VIDEO_SNAPSHOT : 创建视视频录制时截屏的请求
> 5. TEMPLATE_ZERO_SHUTTER_LAG : 创建一个适用于零快门延迟的请求。在不影响预览帧率的情况下最大化图像质量
> 6. TEMPLATE_MANUAL : 创建一个基本捕获请求，这种请求中所有的自动控制都是禁用的(自动曝光，自动白平衡、自动焦点)

- **createCaptureSession(List<Surface> outputs,CameraCaptureSession.StateCallback callback,Handler handler)**：
  创建CaptureSession会话。第一个参数 outputs 是一个 List 数组，相机会把捕捉到的图片数据传递给该参数中的 Surface 。第二个参数 StateCallback 是创建会话的状态回调。第三个参数描述了 StateCallback 被调用时所在的线程

## 3. CameraCharacteristics

描述摄像头的各种特性，类似于Camera1中的CamerInfo。通过CameraManager的getCameraCharacteristics(String cameraId)方法来获取

- **get(Key<T> key)** ：
  通过制定的key获取相应的相机参数。

常用的key值有：

> 1. CameraCharacteristics.LENS_FACING :
>    获取摄像头方向。前置摄像头（LENS_FACING_FRONT）或 后置摄像头（LENS_FACING_BACK）
> 2. CameraCharacteristics.INFO_SUPPORTED_HARDWARE_LEVEL：
>    获取当前设备支持的相机特性
> 3. CameraCharacteristics.SENSOR_ORIENTATION：
>    获取摄像头方向
> 4. CameraCharacteristics.SCALER_STREAM_CONFIGURATION_MAP：
>    获取StreamConfigurationMap，它是管理摄像头支持的所有输出格式和尺寸
> 5. CameraCharacteristics.FLASH_INFO_AVAILABLE：
>    是否支持闪光灯
> 6. CameraCharacteristics.STATISTICS_INFO_MAX_FACE_COUNT:
>    同时检测到人脸的数量
> 7. CameraCharacteristics.STATISTICS_INFO_AVAILABLE_FACE_DETECT_MODES:
>    相机支持的人脸检测模式

## 4. CaptureRequest

描述了一次操作请求，拍照、预览等操作都需要先传入CaptureRequest参数，具体的参数控制也是通过CameraRequest的成员变量来设置

- **addTarget(Surface outputTarget)**：
  给此次请求添加一个Surface对象作为图像的输出目标
- **set(Key<T> key, T value)**：
  设置指定的参数值。

> // 自动对焦
> captureRequestBuilder.set(CaptureRequest.CONTROL_AF_MODE, CaptureRequest.CONTROL_AF_MODE_CONTINUOUS_PICTURE)
> // 闪光灯
> captureRequestBuilder.set(CaptureRequest.CONTROL_AE_MODE, CaptureRequest.CONTROL_AE_MODE_ON_AUTO_FLASH)
> // 根据摄像头方向对保存的照片进行旋转，使其为"自然方向"
> captureRequestBuilder.set(CaptureRequest.JPEG_ORIENTATION, mCameraSensorOrientation)
> // 人脸检测模式
> captureRequestBuilder.set(CaptureRequest.STATISTICS_FACE_DETECT_MODE, CameraCharacteristics.STATISTICS_FACE_DETECT_MODE_SIMPLE)

## 5. CameraCaptureSession

当需要拍照、预览等功能时，需要先创建该类的实例，然后通过该实例里的方法进行控制（例如：拍照 capture()）

- **setRepeatingRequest(CaptureRequest request,
  CaptureCallback listener, Handler handler)**：
  根据传入的 CaptureRequest 对象开始一个无限循环的捕捉图像的请求。第二个参数 listener 为捕捉图像的回调，在回调中可以拿到捕捉到的图像信息
- **capture( CaptureRequest request,
  CaptureCallback listener, Handler handler)**：
  拍照。第二个参数为拍照的结果回调

## 6. CaptureResult

描述拍照完成后的结果

## 7. ImageReader

用于接收拍照结果和访问拍摄照片的图像数据。
得到一个ImageReader对象的方法为newInstance(int width, int height, int format, int maxImages)。前两个参数是保存图片的宽高，第三个参数为保存图片的格式，第四个参数代表用户可以同时访问到的最大图片数量

> 注意：
> 这个参数应该根据具体需业务需求尽可能的小，因为它的数值越大意味着需要消耗的内存就越高

- **acquireNextImage()**：
  得到ImageReader图像队列中的下一张图片，返回值是一个Image对象

## 8. Image

一个完整的图片缓存

- **getPlanes()**：
  获取该图像的像素平面数组。这个数组的大小跟图片的格式有关，如 JPEG格式数组大小为1

## 9. Plane

图像数据的单色平面

- **getBuffer()**：
  获取包含帧数据的ByteBuffer。通过这个ByteBuffer我们就可以把图片保存下来











### 点击跳转到图库

```
Intent intent = new Intent(Intent.ACTION_VIEW);
Uri data = Uri.parse("content://media/external/images/media/");
long thumbNailsId = 449;
data = ContentUris.withAppendedId(data, thumbNailsId);
intent.setDataAndType(data,"image/*");
startActivity(intent);
```

### 储存图片

```
path  = getPhotoFileName();
cameraFile = createNewFile(path);
//文件储存
mFile = new File(cameraFile.toURI());

public static final String SEPARATOR = System.getProperty("file.separator");
public static final String EXTERNAL_STORAGE_DIRECTORY_ROOT =
           Environment.getExternalStorageDirectory().getAbsolutePath()+ SEPARATOR + "DCIM" + SEPARATOR+ "Image" + SEPARATOR ;

	public String getPhotoFileName() {
        String status = Environment.getExternalStorageState();
        if (!status.equals(Environment.MEDIA_MOUNTED)) {
            //showToast(R.string.camera_error_no_sdcard);
        } else {
            try {
                filePath = EXTERNAL_STORAGE_DIRECTORY_ROOT + getWorkCirlePhotoFileName();
            }catch (Exception e)
            {
                e.printStackTrace();
            }
        }
        return filePath ;
    }
    
    //路径
    public static File createNewFile(String path) {
        File file = new File(path);
        try {
           /* if (!file.getParentFile().exists()) {
                file.getParentFile().mkdirs();
            }*/

            if (!file.exists()) {
                file.mkdirs();
            }
            if (file.exists()) {
                file.delete();
            }
            file.createNewFile();
        } catch (IOException e) {
            //LogUtil.e(TAG, e.getMessage());
        }
        return file;
    }
    
    //给每张照片命名
    public static String getWorkCirlePhotoFileName() {
        Date date = new Date(System.currentTimeMillis());
        SimpleDateFormat dateFormat = new SimpleDateFormat(
                "'IMG'_yyyy-MM-dd-HH-mm-ss", Locale.CHINA);
        return dateFormat.format(date) + ".jpg";
    }
```

