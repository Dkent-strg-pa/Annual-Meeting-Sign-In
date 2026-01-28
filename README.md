# Event Sign-In Form

A simple, accessible sign-in form that works without requiring any user accounts. Perfect for events where attendees may not have Google accounts or other login credentials.

## Features

- ✅ **No login required** - Works for anyone with internet access
- ✅ **Mobile-friendly** - Responsive design that works on all devices
- ✅ **Large, clear form fields** - Easy for older attendees to use
- ✅ **Accessible** - Works with screen readers and keyboard navigation
- ✅ **Professional design** - Clean, modern interface

## Setup Instructions

### Option 1: Using Formspree (Recommended - Free tier available)

1. **Create a Formspree account** (free at https://formspree.io)
   - Sign up for a free account
   - Create a new form
   - Copy your form endpoint URL (looks like `https://formspree.io/f/YOUR_FORM_ID`)

2. **Update the form action**
   - Open `sign-in-form.html`
   - Find the line: `<form id="signInForm" action="https://formspree.io/f/YOUR_FORM_ID" method="POST">`
   - Replace `YOUR_FORM_ID` with your actual Formspree form ID

3. **Host the HTML file**
   - Upload `sign-in-form.html` to any web hosting service:
     - **Netlify Drop** (drag & drop): https://app.netlify.com/drop
     - **GitHub Pages**: Upload to a GitHub repo and enable Pages
     - **Vercel**: Drag and drop the file
     - **Your own web server**: Upload via FTP
   - Get the public URL (e.g., `https://your-event-signin.netlify.app`)

4. **Create QR Code**
   - Use any QR code generator (Google "QR code generator")
   - Enter your form URL
   - Download and print the QR code

### Option 2: Using Netlify Forms (No account needed for users, but you need Netlify)

1. **Add Netlify form attributes**
   - Change the form tag to: `<form id="signInForm" netlify name="sign-in" method="POST">`
   - Add `data-netlify="true"` attribute

2. **Deploy to Netlify**
   - Drag and drop the HTML file to Netlify Drop
   - Netlify will automatically handle form submissions
   - View submissions in your Netlify dashboard

### Option 3: Using Microsoft Forms (Alternative)

Microsoft Forms allows anonymous submissions:
1. Go to https://forms.microsoft.com
2. Create a new form
3. Click "Settings" → Enable "Anyone can respond"
4. Share the form link
5. Generate QR code from that link

## Customization

You can easily customize the form by editing `sign-in-form.html`:

- **Change form fields**: Add or remove input fields in the HTML
- **Modify colors**: Update the CSS gradient colors (currently purple)
- **Adjust styling**: Modify the CSS in the `<style>` tag
- **Add/remove fields**: Edit the form groups as needed

## Testing

1. Open `sign-in-form.html` in a web browser
2. Fill out the form and submit
3. Check your Formspree/Netlify dashboard for the submission

## Privacy Note

This form collects user data and sends it to Formspree/Netlify. Make sure to:
- Comply with privacy regulations (GDPR, etc.)
- Add a privacy notice if required
- Inform users how their data will be used
