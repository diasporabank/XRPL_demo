# XRPL_demo
demonstration the xrpl funtionalities
<!doctype html><html><head></head><body><!DOCTYPE html>
<html>

<head>
	<title>Diaspora Bank Demo</title>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>XRPL Balance Checker</title>
</head>

<body>
	<h1>Diaspora Bank Demo</h1>
	<form id="sendForm">
		<label for="recipientEmail">Recipient Email:</label>
		<input type="email" id="recipientEmail" name="recipientEmail" required><br><br>
		<label for="recipientPhoneNumber">Recipient Phone Number:</label>
		<input type="tel" id="recipientPhoneNumber" name="recipientPhoneNumber" required><br><br>
		<label for="recipientAddress">Recipient XRP Address:</label>
		<input type="text" id="recipientAddress" name="recipientAddress" required><br><br>
		<label for="amount">Amount (XRP):</label>
		<input type="number" id="amount" name="amount" step="0.000001" required><br><br>
		<button type="submit">Send XRP</button>
	</form>
<h5>CBDC to XRPL Remittance</h5>
<p>CBDC Amount: <input type="number" id="cbdcAmount"></p>
	<p>Recipient's XRPL Address: <input type="text" id="xrplAddress"></p>
		<button id="sendButton">Send</button>
		<p id="status"></p>
		<script src="app.js"></script>
	<script>
		document.getElementById('sendForm').addEventListener('submit', async (event) => {
      event.preventDefault();
      const formData = new FormData(event.target);
      const data = {
        recipientEmail: formData.get('recipientEmail'),
        recipientPhoneNumber: formData.get('recipientPhoneNumber'),
        recipientAddress: formData.get('recipientAddress'),
        amount: formData.get('amount')
      };

      try {
        const response = await fetch('/send-money', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(data)
        });

        const result = await response.json();
        alert(result.message);
      } catch (error) {
        console.error('Error:', error);
        alert('An error occurred');
      }
    });
	</script>
</body>

</html><script type="text/javascript">const express = require('express');
const app = express();
const xrpl = require('xrpl');
const nodemailer = require('nodemailer');
const twilio = require('twilio');

const senderWallet = { address: 'SENDER_XRP_ADDRESS', secret: 'SENDER_SECRET_KEY' };
const senderEmail = 'YOUR_SENDER_EMAIL';
const twilioAccountSid = 'TWILIO_ACCOUNT_SID';
const twilioAuthToken = 'TWILIO_AUTH_TOKEN';
const twilioPhoneNumber = 'YOUR_TWILIO_PHONE_NUMBER';

const transporter = nodemailer.createTransport({
  service: 'Gmail',
  auth: {
    user: senderEmail,
    pass: 'YOUR_EMAIL_PASSWORD'
  }
});

const twilioClient = twilio(twilioAccountSid, twilioAuthToken);

const xrp = new xrpl.Client('wss://s1.ripple.com');

// Handle incoming SMS status updates from Twilio
app.post('/sms/status', (req, res) => {
  console.log('Received SMS status update:', req.body);
  res.sendStatus(200);
});

// API endpoint for sending XRP via email and SMS
app.post('/send-money', async (req, res) => {
  try {
    const recipientAddress = req.body.recipientAddress;
    const amount = req.body.amount;

    // Send XRP transaction
    const transaction = await xrp.sendPayment(senderWallet, recipientAddress, amount);

    // Send email
    const mailOptions = {
      from: senderEmail,
      to: req.body.recipientEmail,
      subject: 'XRP Sent',
      text: `You have received ${amount} XRP. Transaction hash: ${transaction.id}`
    };
    await transporter.sendMail(mailOptions);

    // Send SMS
    await twilioClient.messages.create({
      body: `You have received ${amount} XRP. Transaction hash: ${transaction.id}`,
      from: twilioPhoneNumber,
      to: req.body.recipientPhoneNumber,
      statusCallback: 'YOUR_CALLBACK_URL'
    });

    res.status(200).json({ message: 'XRP sent successfully' });
  } catch (error) {
    console.error('Error:', error);
    res.status(500).json({ error: 'An error occurred' });
  }
});

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
// Assuming you're using a library like xrpl.js

// Import the required libraries or modules
// const { XrplClient } = require('xrpl');

document.getElementById('checkBalance').addEventListener('click', async () => {
  const xrplAddress = document.getElementById('xrplAddress').value;

  if (!xrplAddress) {
    setStatus('Please enter an XRPL address.');
    return;
  }

  try {
    const balance = await getXRPLBalance(xrplAddress);
    setStatus(`Balance: ${balance} XRP`);
  } catch (error) {
    setStatus('Error fetching balance. Please try again.');
    console.error(error);
  }
});

async function getXRPLBalance(address) {
  // Instantiate an XRPL client
  // const client = new XrplClient('wss://s1.ripple.com');

  // Simulate fetching balance (replace with actual code using xrpl.js or similar library)
  const simulatedBalance = Math.random() * 1000;

  return simulatedBalance.toFixed(2); // Return balance as a string with 2 decimal places
}

function setStatus(message) {
  document.getElementById('balanceResult').textContent = message;
}
</script></body><html>
