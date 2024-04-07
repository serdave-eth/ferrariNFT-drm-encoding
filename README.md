## Description

cenc_drm_content_protection_h264.py: encodes a video file in H.264 codec. Only encodes up to 1080p. Use this for DASH and HLS.

cenc_drm_content_protection_h265.py: encodes a video file in H.265 codec. Encodes up to 4k. Use this only for HLS.

cenc_drm_content_protection_vp9.py: encodes a video file in VP9 codec. Encodes up to 4k. Use this for DASH and HLS.

demo.js: code for instantiating bitmovin player with correct stream file based on browser and other logic.

## How to run the script

Below is an example of the script with sensitive information removed:

python3 cenc_drm_content_protection_h264.py --BITMOVIN_API_KEY= --HTTP_INPUT_HOST="" --HTTP_INPUT_FILE_PATH="" --S3_OUTPUT_BUCKET_NAME="" --S3_OUTPUT_ACCESS_KEY="" --S3_OUTPUT_SECRET_KEY="" --S3_OUTPUT_BASE_PATH="" --DRM_KEY="" --DRM_WIDEVINE_KID="" --DRM_WIDEVINE_PSSH="" --DRM_PLAYREADY_LAURL="" --DRM_FAIRPLAY_URI="" --DRM_FAIRPLAY_IV="" --IS_DASH="F"

A description of each input can be found at the top of cenc_drm_content_protection_h264.py.

The script works with the video stored as an S3 input. Instructions on the setup can be found here: https://hs.ezdrm.com/hubfs/Documentation/EZDRM%20Bitmovin%20Encoding%20V2.pdf?hsLang=en

A couple "gotchas" in the guide:

1. For the S3 bucket, you need to have Object Ownership has ACL enabled. 
2. For the S3 bucket, you need to have CORS access. This is the easiest CORS access to have: 

[
    {
        "AllowedHeaders": ["*"],
        "AllowedMethods": ["GET", "HEAD"],
        "AllowedOrigins": ["*"],
        "ExposeHeaders": [],
        "MaxAgeSeconds": 3000
    }
]

CORS access can be found under the Permissions tab for the S3 bucket.

If the script runs successfully, you'll see this:

Encoding status is Status.FINISHED (progress: 100 %)
Encoding finished successfully

If you are running this script with HLS manifest (i.e. for Apple devices) you need to use a different DRM_KEY than for DASH

The variable "EXAMPLE_NAME" at the top of the script is the directory the script will save the outputs.

## Running the script in production

For each video we need to create 4 encoded outputs:

1. DASH, H.264
2. HLS, H.264
3. DASH, VP9
4. HLS, H.265

## Instantiating the video player and choosing the right stream file

Please look at demo.js. Below is the pseudocode for the logic we need to build:

if(browser is Safari) {
    if(mime type supports h265) {
        choose h265 HLS file
    }
    else {
        choose h264 HLS file
    }
}

else {
    if(mime type supports vp9) {
        choose vp9 DASH file
    }
    else {
        choose h264 DASH file
    }
}

## Testing the encoded video

Go to https://bitmovin.com/demos/drm

1. Select "Dash"
2. Manifest URL: the url of the s3 object of the .mpd file (can be found in the output S3 bucket). 
3. License Server URL: Widevine Server URL that EZDRM produces in step 2 of the EZDRM guide. 

## Notes

Original script: https://github.com/bitmovin/bitmovin-api-sdk-examples/blob/main/python/src/cenc_drm_content_protection.py
