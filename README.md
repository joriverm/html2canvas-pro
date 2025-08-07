<p align="center">
<img src="https://raw.githubusercontent.com/yorickshan/html2canvas-pro/main/docs/public/logo.png" height="150">
</p>
<h1 align="center">
html2canvas-pro
</h1>
<p align="center">
Next generation JavaScript screenshots tool.
<p>
<p align="center">
  <a href="https://github.com/yorickshan/html2canvas-pro/actions/workflows/ci.yml"><img src="https://github.com/yorickshan/html2canvas-pro/actions/workflows/ci.yml/badge.svg?branch=main" alt="build status"></a>
  <a href=https://npm.im/html2canvas-pro><img src="https://badgen.net/npm/v/html2canvas-pro" alt="npm version"></a>
  <a href=http://npm.im/html2canvas-pro><img src="https://badgen.net/npm/dm/html2canvas-pro" alt="npm downloads"></a>
  <a href="https://www.jsdelivr.com/package/npm/html2canvas-pro"><img src="https://data.jsdelivr.com/v1/package/npm/html2canvas-pro/badge" /></a>
<p>
<p align="center">
 <a href="https://yorickshan.github.io/html2canvas-pro/">Documentation</a> | <a href="https://yorickshan.github.io/html2canvas-pro/getting-started.html">Getting Started</a> | <a href="https://yorickshan.github.io/html2canvas-pro/configuration.html">Configuration</a>
</p>
<br>

html2canvas-pro allows you to take "screenshots" of DOM elements directly in the browser. It reads the DOM and CSS styles of an element and renders them into a canvas, providing a powerful solution for generating images, PDFs, or enabling print functionality in web applications.

## 🚀 Why html2canvas-pro?

html2canvas-pro is an actively maintained fork of [niklasvh/html2canvas](https://github.com/niklasvh/html2canvas) with enhanced features and modern browser support:

### ✨ New Features
- **Modern CSS Color Functions**: Support for `color()`, `lab()`, `lch()`, `oklab()`, `oklch()` including relative colors
- **Enhanced Image Handling**: Full support for `object-fit` property on `<img>` elements
- **WebKit Text Stroke**: Proper handling of `-webkit-text-stroke` alignment
- **TypeScript First**: Built with TypeScript for better developer experience

### 🐛 Bug Fixes
- Improved cross-origin image handling
- Better CSS parsing and rendering accuracy
- Enhanced performance and memory usage
- See [CHANGELOG](./CHANGELOG.md) for complete list of fixes

If you found this helpful, don't forget to leave a star 🌟.

## 📦 Installation

```bash
npm install html2canvas-pro
# or
yarn add html2canvas-pro
# or
pnpm add html2canvas-pro
```

### CDN Usage
```html
<script src="https://cdn.jsdelivr.net/npm/html2canvas-pro@latest/dist/html2canvas-pro.min.js"></script>
```

## 🎯 Quick Start

### Basic Usage
```javascript
import html2canvas from 'html2canvas-pro';

// Capture the entire body
html2canvas(document.body).then(canvas => {
    document.body.appendChild(canvas);
});

// Capture a specific element
const element = document.getElementById('myElement');
html2canvas(element).then(canvas => {
    // Convert to image and download
    const link = document.createElement('a');
    link.download = 'screenshot.png';
    link.href = canvas.toDataURL();
    link.click();
});
```

### Advanced Examples

#### Download as Image
```javascript
async function downloadScreenshot(element, filename = 'screenshot.png') {
    const canvas = await html2canvas(element, {
        backgroundColor: null, // Transparent background
        scale: 2, // Higher resolution
        useCORS: true // Handle cross-origin images
    });
    
    const link = document.createElement('a');
    link.download = filename;
    link.href = canvas.toDataURL('image/png');
    link.click();
}

// Usage
downloadScreenshot(document.getElementById('report'));
```

#### Custom Styling & Cropping
```javascript
const canvas = await html2canvas(element, {
    backgroundColor: '#ffffff',
    width: 800,
    height: 600,
    x: 0,
    y: 0,
    scale: 1,
    onclone: (clonedDoc) => {
        // Modify the cloned document before rendering
        const clonedElement = clonedDoc.getElementById('dynamic-content');
        if (clonedElement) {
            clonedElement.style.display = 'block';
        }
    }
});
```

#### Generate PDF (using jsPDF)
```javascript
import jsPDF from 'jspdf';

async function generatePDF(element) {
    const canvas = await html2canvas(element, {
        scale: 2,
        useCORS: true
    });
    
    const imgData = canvas.toDataURL('image/png');
    const pdf = new jsPDF();
    const imgWidth = 210;
    const pageHeight = 295;
    const imgHeight = (canvas.height * imgWidth) / canvas.width;
    let heightLeft = imgHeight;
    
    let position = 0;
    pdf.addImage(imgData, 'PNG', 0, position, imgWidth, imgHeight);
    heightLeft -= pageHeight;
    
    while (heightLeft >= 0) {
        position = heightLeft - imgHeight;
        pdf.addPage();
        pdf.addImage(imgData, 'PNG', 0, position, imgWidth, imgHeight);
        heightLeft -= pageHeight;
    }
    
    pdf.save('document.pdf');
}
```

## ⚙️ Configuration Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `allowTaint` | boolean | `false` | Allow cross-origin images to taint the canvas |
| `backgroundColor` | string\|null | `#ffffff` | Canvas background color (`null` for transparent) |
| `canvas` | HTMLCanvasElement | `null` | Existing canvas element to use |
| `foreignObjectRendering` | boolean | `false` | Use ForeignObject rendering if supported |
| `ignoreElements` | function | `() => false` | Function to ignore specific elements |
| `imageTimeout` | number | `15000` | Timeout for loading images (ms) |
| `logging` | boolean | `true` | Enable debug logging |
| `onclone` | function | `null` | Callback when document is cloned |
| `proxy` | string | `null` | URL for proxy server (for cross-origin images) |
| `scale` | number | `devicePixelRatio` | Rendering scale |
| `useCORS` | boolean | `false` | Attempt to load images using CORS |
| `width` | number | element width | Canvas width |
| `height` | number | element height | Canvas height |
| `x` | number | `0` | Crop x-coordinate |
| `y` | number | `0` | Crop y-coordinate |

For complete configuration details, see the [Configuration Documentation](https://yorickshan.github.io/html2canvas-pro/configuration.html).

## 🌐 Browser Compatibility

| Browser | Minimum Version | Notes |
|---------|----------------|-------|
| Chrome | 65+ | Full support |
| Firefox | 58+ | Full support |
| Safari | 12+ | Full support |
| Edge | 79+ | Full support |
| IE | Not supported | Use html2canvas instead |

## 💡 Performance Tips

1. **Optimize DOM structure**: Reduce complex nesting and unnecessary elements
2. **Use scale wisely**: Higher scales increase quality but impact performance
3. **Implement caching**: Cache results for elements that don't change frequently
4. **Handle large elements**: Use window size options for very large elements

```javascript
// For large elements
await html2canvas(largeElement, {
    windowWidth: largeElement.scrollWidth,
    windowHeight: largeElement.scrollHeight,
    scale: 1 // Start with scale 1 for better performance
});
```

## 🔧 Troubleshooting

### Images not rendering?
- Ensure images are from the same origin or use `useCORS: true`
- For cross-origin images, set up a [proxy server](https://yorickshan.github.io/html2canvas-pro/proxy.html)
- Check that `allowTaint: false` isn't blocking images

### Canvas empty or cut off?
- Check browser canvas size limits (32,767px max dimension)
- Use window configuration for large elements
- Ensure element is visible and has content

### Styling issues?
- Use `onclone` callback to modify styles in the cloned document
- Check for CSS properties that aren't [supported](https://yorickshan.github.io/html2canvas-pro/features.html)
- Test with `foreignObjectRendering: true` for complex layouts

### Performance issues?
- Reduce element complexity and DOM depth
- Lower the scale factor
- Use `removeContainer: true` (default) to clean up temporary elements

## 🤝 Contributing

We welcome contributions! Here's how you can help:

1. **Bug Reports**: Open an issue with reproduction steps
2. **Feature Requests**: Describe your use case and proposed solution
3. **Pull Requests**: 
   - Fork the repository
   - Create a feature branch: `git checkout -b feature/your-feature`
   - Make your changes and add tests
   - Run tests: `npm test`
   - Submit a pull request

### Development Setup
```bash
git clone https://github.com/yorickshan/html2canvas-pro.git
cd html2canvas-pro
npm install
npm run build
npm test
```

## 📚 API Reference

For detailed API documentation, examples, and advanced usage patterns, visit our [comprehensive documentation](https://yorickshan.github.io/html2canvas-pro/).

## 📄 License

[MIT](LICENSE) - feel free to use in personal and commercial projects.

## 🙏 Acknowledgments

- Original [html2canvas](https://github.com/niklasvh/html2canvas) by Niklas von Hertzen
- All contributors who have helped improve this library
