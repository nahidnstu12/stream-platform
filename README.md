# Video Streaming Project Documentation

This project allows you to upload and stream videos on the web. It includes multiple versions with different streaming techniques.

## Version 1: Basic Chunked Streaming

In this version, videos are served chunk by chunk using the `fileStream` API. Each chunk is 1MB by default, but this can be configured.

### How It Works

- **Chunk Size**: The video is divided into chunks of 1MB.
- **Range Requests**: When a client requests a specific range of bytes, the server responds with that portion of the video.
  - The server reads the `Range` header from the request to determine the start and end bytes.
  - If the requested range is valid, the server sends back the requested chunk with appropriate headers, including `Content-Range` and `Content-Length`.
  - If the range is not valid (e.g., the start byte is beyond the end of the file), the server responds with a `416` status code indicating the range is not satisfiable.
- **Full Video Requests**: If no range is specified, the server sends the entire video file at once.

### Key Endpoints

- **Serve the Video File**:
  - Endpoint: `/video`
  - Description: Serves the video file with 1MB chunkwise streaming API.

### Usage Instructions

1. **Install Dependencies**:
   ```sh
   pnpm install
   ```
2. **Start the Server**:
   ```sh
   pnpm dev:v1
   ```
3. **Open the Client**:
   - Use the `live server` extension in VS Code to open `index.html`.

## Version 2: Optimized Streaming with DASH

This version improves upon the first by using DASH (Dynamic Adaptive Streaming over HTTP) and H.264 compression, reducing the file size while maintaining video quality. This version serves video chunks using the DASH media player.

### How It Works

- **DASH (Dynamic Adaptive Streaming over HTTP)**: A streaming protocol that allows the video to be divided into small segments or chunks, each with its own URL.
  - **MPD (Media Presentation Description)**: A manifest file that lists all the available video segments and their URLs.
  - **Segments**: The video is split into multiple segments, allowing the client to request only the parts it needs at a given time.
  - **Adaptive Bitrate Streaming**: DASH can adjust the quality of the video stream in real-time based on the client’s network conditions.
- **H.264 Compression**: A video compression standard that reduces the file size without compromising quality.
- **Rate Limiting**: To control the bandwidth usage, the server applies rate limiting to the requests for the MPD file and video segments.

### Key Endpoints

- **Serve the DASH Manifest File**:
  - Endpoint: `/video.mpd`
  - Description: Serves the DASH manifest file with rate limiting.
  - Middleware: `videoRateLimitMpd` to limit the rate of requests.
  
- **Serve the DASH Segment Files**:
  - Endpoint: `/:segment`
  - Description: Serves the video segment files with rate limiting.
  - Middleware: `videoRateLimitSegments` to limit the rate of requests.
  - Parameter: `segment` - The name of the segment file to be served.

### Usage Instructions

1. **Install Dependencies**:
   ```sh
   pnpm install
   ```
2. **Start the Server**:
   ```sh
   pnpm dev:v2
   ```
3. **Open the Client**:
   - Use the `live server` extension in VS Code to open `dash-player.html`.


## General Notes

- **Live Server Extension**: Ensure you have the `live server` extension installed in VS Code for opening HTML files.
- **Configuration**: Chunk size, rate limits, and server ports can be adjusted as needed.
- **Error Handling**: The server includes basic error handling to respond appropriately if an invalid range is requested or if there are issues with the video file.

This documentation provides a high-level overview of how each version of the video streaming project works and how to set it up and run it.