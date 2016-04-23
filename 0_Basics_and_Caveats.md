
Caveats
--------------------------------------------------

### GPU Usage 
With a single GPU, running multiple TensorFlow instance could cause crashes (`CUDA_ERROR_OUT_OF_MEMORY`).  To prevent this,
it is recommended to specify the GPU to be allocated:

    gpu_options = tf.GPUOptions(per_process_gpu_memory_fraction=0.333) # use one-third of GPU memory
    session = tf.Session(config=tf.ConfigProto(gpu_options=gpu_options)

