# Hanwha Camera Webhook Research Checklist

## Project Goal
Send webhook to n8n server on motion detection within a bounding box

---

## Phase 1: Initial Research & Validation

### Camera Capabilities Assessment
- [ ] Document exact Hanwha camera model number
- [ ] Check camera datasheet for "Open Platform" support
- [ ] Verify current firmware version
- [ ] Check if firmware update is available/needed

### Native Feature Investigation
- [ ] Log into camera web interface
- [ ] Navigate to Setup → Event settings
- [ ] Look for "Virtual Line" or "Area Detection" analytics
- [ ] Check if "HTTP Notification" or "Webhook" option exists in Actions
- [ ] Test drawing a bounding box in native interface
- [ ] Document what native event triggers are available

### Network & Connectivity
- [ ] Confirm camera has network access to n8n server
- [ ] Test basic connectivity (ping from camera network)
- [ ] Document camera IP address
- [ ] Document n8n server IP/hostname and port
- [ ] Check firewall rules between camera and n8n

---

## Phase 2: n8n Webhook Setup

### Webhook Configuration
- [ ] Create new n8n workflow
- [ ] Add Webhook trigger node
- [ ] Configure webhook URL path (e.g., `/webhook/hanwha-motion`)
- [ ] Set authentication method (none, header auth, or URL token)
- [ ] Set response mode to "Immediately"
- [ ] Note full webhook URL for camera configuration

### Testing & Validation
- [ ] Test webhook with curl command:
```bash
curl -X POST http://your-n8n:5678/webhook/hanwha-motion \
  -H "Content-Type: application/json" \
  -d '{"camera_id":"TEST","timestamp":"2025-01-10T12:00:00Z","zone":"box1"}'
```
- [ ] Verify n8n receives and logs the test payload
- [ ] Design downstream workflow actions (notifications, logging, etc.)
- [ ] Test complete n8n workflow end-to-end with mock data

---

## Phase 3: Native Integration (Try This First)

### Camera Configuration
- [ ] Configure motion detection bounding box in camera UI
- [ ] Set sensitivity and detection parameters
- [ ] Configure HTTP notification if available:
  - [ ] Enter n8n webhook URL
  - [ ] Set HTTP method to POST
  - [ ] Configure any required headers
  - [ ] Set payload format (if customizable)
- [ ] Enable the event rule
- [ ] Test trigger by creating motion in bounding box
- [ ] Verify webhook received in n8n

### If Native Works
- [ ] Document exact camera settings used
- [ ] Test reliability over 24-48 hours
- [ ] Monitor for false positives/negatives
- [ ] **STOP HERE - No custom code needed!**

---

## Phase 4: Open Platform Investigation (Only if Native Fails)

### SDK Access
- [ ] Register for Hanwha developer portal account
- [ ] Request Open Platform SDK access
- [ ] Download SDK package once approved
- [ ] Review SDK documentation and API reference
- [ ] Check sample applications included

### Development Environment Setup
- [ ] Install required IDE (Visual Studio/Eclipse)
- [ ] Install cross-compilation toolchain
- [ ] Set up SDK build environment
- [ ] Compile and deploy "Hello World" sample app
- [ ] Verify sample app runs on camera

### Analytics Event Research
- [ ] Identify API calls for area detection events
- [ ] Document event data structure available
- [ ] Research HTTP/libcurl availability in SDK
- [ ] Review networking examples in SDK samples

---

## Phase 5: Custom Application Development (If Required)

### Core Functionality
- [ ] Create basic application framework
- [ ] Implement analytics event subscription
- [ ] Add bounding box filter logic
- [ ] Implement JSON payload builder
- [ ] Add HTTP POST function using libcurl
- [ ] Implement error handling and logging

### Network Resilience
- [ ] Add retry logic for failed webhook calls
- [ ] Implement connection timeout handling
- [ ] Add event queuing for offline scenarios
- [ ] Log failed attempts for debugging

### Testing & Deployment
- [ ] Test locally with SDK emulator (if available)
- [ ] Deploy to camera hardware
- [ ] Monitor application logs
- [ ] Trigger test events and verify n8n receipt
- [ ] Stress test with multiple rapid events
- [ ] Document any issues and resolutions

---

## Phase 6: Production Deployment

### Finalization
- [ ] Document complete configuration
- [ ] Create backup of camera settings
- [ ] Set up monitoring/alerting for webhook failures
- [ ] Train relevant users if needed
- [ ] Schedule periodic testing to verify ongoing operation

### Optional Enhancements
- [ ] Add authentication token to webhook
- [ ] Implement payload encryption if needed
- [ ] Add camera snapshot/image capture on event
- [ ] Scale to multiple cameras if applicable

---

## Notes & Findings

### Camera Model Information
- Model: 
- Firmware: 
- Open Platform Support: 

### Useful Links
- Hanwha Developer Portal: 
- Camera Web Interface: 
- n8n Webhook URL: 

### Decision Log
- Date: 
- Decision: (Native vs Open Platform)
- Reason: 

---

## Resources Needed
- [ ] Camera admin credentials
- [ ] Network diagram/documentation
- [ ] n8n server access
- [ ] Hanwha technical support contact (if needed)
- [ ] Time estimate: _____ hours

---

**Project Status:** Planning Phase
**Last Updated:** 2025-01-10
