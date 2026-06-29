name: Fetch StarHub Poster Image

on:
  workflow_dispatch:
    inputs:
      id_code:
        description: 'Enter the CT code (e.g., CT0000557980)'
        required: true
        default: 'CT0000557980'

jobs:
  download-poster:
    runs-on: ubuntu-latest
    steps:
      - name: Build URL and Download Image
        run: |
          # 1. Take the user input and build the correct URL
          CODE="${{ github.event.inputs.id_code }}"
          URL="https://poster.starhubgo.com/poster/GLOBAL_${CODE}/GLOBAL_${CODE}_2.jpg"
          
          echo "========================================="
          echo "Target URL: $URL"
          echo "========================================="
          
          # 2. Try to download the image
          curl -s -f -o poster.jpg "$URL"
          
          # 3. Check if the image actually exists
          if [ $? -eq 0 ]; then
            echo "✅ Success! Image downloaded successfully."
          else
            echo "❌ Error: Could not find an image for code '${CODE}'. Please check the code and try again."
            exit 1
          fi

      - name: Upload Image to GitHub Logs
        uses: actions/upload-artifact@v4
        with:
          name: downloaded-poster
          path: poster.jpg
