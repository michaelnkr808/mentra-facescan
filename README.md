# MentraOS Face Scanner

A MentraOS application that captures photos via voice commands and provides audio feedback.

## Features

- Voice-activated photo capture
- Text-to-speech audio feedback
- Real-time transcription processing
- Visual feedback through text wall display

## Prerequisites

- [Bun](https://bun.sh) v1.3.3 or higher
- MentraOS device with camera and audio capabilities
- MentraOS API credentials

## Setup

1. Clone the repository
2. Install dependencies:
```bash
bun install
```

3. Create a `.env` file in the root directory:
```env
PACKAGE_NAME=your-package-name
MENTRAOS_API_KEY=your-api-key
PORT=3000
```

## Running the App
```bash
bun run index.ts
```

## Usage

1. Start the application
2. Connect your MentraOS device
3. The app will greet you with "Hello from your app!"
4. Say **"What's your name"** to trigger photo capture
5. Photos are captured and logged to the console

## Voice Commands

- **"What's your name"** - Captures a photo using the device camera

## Troubleshooting

### Audio not working
- Ensure device volume is turned up
- Check MentraOS audio permissions
- Verify the session is fully initialized before audio playback

### Photo capture fails
- Verify camera permissions in MentraOS
- Check device camera is functioning properly
- Review console logs for specific error messages

## Development

Built with:
- [Bun](https://bun.sh) - JavaScript runtime
- [@mentra/sdk](https://mentra.com) - MentraOS SDK
- TypeScript

## License

[Your License Here]