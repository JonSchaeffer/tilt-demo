# For local Development:
allow_k8s_contexts('k3d-tilt')


# db-database
k8s_yaml('./k8s-specifications/db-deployment.yaml',
  )

k8s_yaml('./k8s-specifications/db-service.yaml',
  )

k8s_resource(
  workload='db',
  port_forwards=['5432:5432'],
)

# redis

k8s_yaml('./k8s-specifications/redis-deployment.yaml',
  )

k8s_yaml('./k8s-specifications/redis-service.yaml',
  )


# Define Docker Builds

# vote
docker_build("dockersamples/examplevotingapp_vote", "./vote", dockerfile="./vote/Dockerfile", target="dev", live_update=[
  sync('./vote', '/usr/local/app'),
])

# result
docker_build("dockersamples/examplevotingapp_result", "./result", live_update=[
  sync('./result', '/usr/local/app'),
])

# worker
docker_build("dockersamples/examplevotingapp_worker", "./worker", live_update=[
  sync('./worker', '/usr/local/app'),
])

# seed

docker_build("dockersamples/examplevotingapp_seed", "./seed-data")

# Define Kubernetes YAML resources
k8s_yaml([
  "./k8s-specifications/vote-deployment.yaml",
  "./k8s-specifications/vote-service.yaml",
  "./k8s-specifications/result-deployment.yaml",
  "./k8s-specifications/result-service.yaml",
  "./k8s-specifications/worker-deployment.yaml",
  #"./k8s-specifications/seed-job.yaml",
])

# Define port forwarding for local dev
k8s_resource("vote", port_forwards=['8082:80'])
k8s_resource("result", port_forwards=['8083:80'])

# Define seed-job resource, but don't auto run
#k8s_resource("seed-job", auto_init=False)


# Automatically watch and rebuild when files change
