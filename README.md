# Fist person view live streamming


# Getting Started 
If you dont know how to use NRSDK. You can see this  [page](https://developer.nreal.ai/develop/unity/android-quickstart) to have a quick start with NRSDK

# Import the NRSDK for First Person View
Import latest SDK in the package.

# Open the Sample Scene
In the Unity Project window, you can find the CameraCaptureDemo sample in: Assets > NRSDK >Demos > RGBCamera-Record.
Replace the VideoCaptureLocalExample with VideoCaptureRTMPExample.

# Build and Run the Sample App
> See `VideoCaptureRTMPExample.cs` , located in `Assets > NRSDK > Demos > Record > Scripts > VideoCaptureRTMPExample` for an example on how to use the video capture. 

~~~c#    

    // A video Capture Example
    public class VideoCaptureRTMPExample : MonoBehaviour
    {
        public NRPreviewer Previewer;

        public string RTMPPath
        {
            get
            {
                return @"rtmp://192.168.69.47:1935/live/movie";
            }
        }
        NRVideoCapture m_VideoCapture = null;

        void Start()
        {
            CreateVideoCaptureTest();
        }

        void Update()
        {
            if (m_VideoCapture == null)
            {
                return;
            }

            if (Input.GetKeyDown(KeyCode.R) || NRInput.GetButtonDown(ControllerButton.TRIGGER))
            {
                StartVideoCapture();

                Previewer.SetData(m_VideoCapture.RecordBehaviour.PreviewTexture, true);
            }

            if (Input.GetKeyDown(KeyCode.T) || NRInput.GetButtonDown(ControllerButton.HOME))
            {
                StopVideoCapture();

                Previewer.SetData(m_VideoCapture.RecordBehaviour.PreviewTexture, false);
            }
        }

        void CreateVideoCaptureTest()
        {
            NRVideoCapture.CreateAsync(false, delegate (NRVideoCapture videoCapture)
            {
                if (videoCapture != null)
                {
                    m_VideoCapture = videoCapture;
                }
                else
                {
                    Debug.LogError("Failed to create VideoCapture Instance!");
                }
            });
        }

        private void StartVideoCapture()
        {
            Resolution cameraResolution = NRVideoCapture.SupportedResolutions.OrderByDescending((res) => res.width * res.height).First();
            Debug.Log(cameraResolution);

            int cameraFramerate = NRVideoCapture.GetSupportedFrameRatesForResolution(cameraResolution).OrderByDescending((fps) => fps).First();
            Debug.Log(cameraFramerate);

            if (m_VideoCapture != null)
            {
                Debug.Log("Created VideoCapture Instance!");
                CameraParameters cameraParameters = new CameraParameters();
                cameraParameters.hologramOpacity = 0.0f;
                cameraParameters.frameRate = cameraFramerate;
                cameraParameters.cameraResolutionWidth = cameraResolution.width;
                cameraParameters.cameraResolutionHeight = cameraResolution.height;
                cameraParameters.pixelFormat = CapturePixelFormat.BGRA32;
                cameraParameters.blendMode = BlendMode.Blend;

                m_VideoCapture.StartVideoModeAsync(cameraParameters,
                    NRVideoCapture.AudioState.ApplicationAndMicAudio,
                    OnStartedVideoCaptureMode);
            }

        }

        private void StopVideoCapture()
        {
            Debug.Log("Stop Video Capture!");
            m_VideoCapture.StopRecordingAsync(OnStoppedRecordingVideo);
        }

        void OnStartedVideoCaptureMode(NRVideoCapture.VideoCaptureResult result)
        {
            Debug.Log("Started Video Capture Mode!");
            m_VideoCapture.StartRecordingAsync(RTMPPath, OnStartedRecordingVideo);
        }

        void OnStoppedVideoCaptureMode(NRVideoCapture.VideoCaptureResult result)
        {
            Debug.Log("Stopped Video Capture Mode!");
        }

        void OnStartedRecordingVideo(NRVideoCapture.VideoCaptureResult result)
        {
            Debug.Log("Started Recording Video!");
        }

        void OnStoppedRecordingVideo(NRVideoCapture.VideoCaptureResult result)
        {
            Debug.Log("Stopped Recording Video!");
            m_VideoCapture.StopVideoModeAsync(OnStoppedVideoCaptureMode);
        }
    }
~~~  

 * Note that the IP address in **RTMPPath** needs to be changed to the IP address of your local server.
 * The "Previewer" is used to preview live images in real time, for debugging purposes.Click the "APP" key of the controller to show or hide it
 * Click the "Trigger" key of the controller to start video capture.


# How to use it step by step
 * Install the application that you have using the video capture feature of NRSDK.
 * Start the rtmpserver in the package.
 * Run your program, and open the video capture function.
 * Use a video player to play the url **"rtmp://serverip:1935/live/movie"**. 
