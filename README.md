# edge-stream-core 
Closed Alpha product: The Data Curation Engine for L4 Robotics &amp; Embodied AI.
`edge-stream` is a zero-copy, ring-buffer engine that sits between your Transport Layer (DDS/NITROS) and your Storage. It buffers high-bandwidth sensor data in RAM/VRAM and only flushes to disk when a semantic trigger fires. Edge Stream is designed to run alongside high-performance transport layers like **NVIDIA NITROS**. If you like to discuss or sign up to alpha, please email: alpha-access@edgestream-core.com. 

- NITROS Compatible: Can tap into NVIDIA NvBufSurface to buffer GPU memory without CPU copying.
- Pre-Event Recall: Configurable "Lookback" window. Capture the 30 seconds leading up to a disengagement.
- VLM/LLM Ready: Designed to feed "High Entropy" clips to Foundation Models (Cosmos, Gemini, GPT-4o).
- Polyglot Triggers: Trigger based on ROS topics, error codes, or external API calls from your inference model.

Configuration (Example)
Define your buffers in a declarative YAML policy:

YAML

# edge_stream_policy.yaml
global:
  storage_path: "/mnt/nvme/events"
  max_disk_usage_gb: 10

buffers:
  - topic: "/sensors/camera/front/nitros_image"
    type: "nvidia::isaac::Image"
    strategy: "gpu_ring_buffer"
    duration: 30s
    
  - topic: "/control/planning/trajectory"
    type: "nav_msgs/msg/Path"
    duration: 60s

triggers:
  - name: "model_uncertainty"
    source: "/perception/yolo/confidence"
    condition: "value < 0.4"
    action:
      snapshot_all: true
      priority: "high"
