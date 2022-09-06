
## Steps to be followed for Deploying Spring Boot Applications to GKE Cluster ##
1. Create GKE cluster
                             
       $ gcloud services enable compute.googleapis.com container.googleapis.com

2. Create a cluster with two n1-standard-1 nodes

       $ gcloud container clusters create sine-gke-cluster --num-nodes 3 --machine-type n1-standard-1 --zone asia-south1-a

3. Run the following command in Cloud Shell to confirm that you are authenticated

        $ gcloud auth list

4. List all configured projects

        $ gcloud config list project
         [core]
         project = deloitte-projectaug-361603

5. If it is not, you can set it with this command:

        $ gcloud config set project <PROJECT_ID>

6. Get the source

       $ git clone https://github.com/azonecloud/gs-springboot-gke.git

       $ cd gs-springboot-gke/

7. Start the Spring Boot app normally with the Spring Boot plugin.(Local Deployment)

       $ mvn -DskipTests spring-boot:run

8. Developing an Docker image for the springbootapp

       $ mvn -DskipTests package

9. Enable Google Container Registry(GCR) to store the container image that you'll create.

       $ gcloud services enable containerregistry.googleapis.com

10. Jib to used,and create the container image and push it to the Container Registry.

        $ export GOOGLE_CLOUD_PROJECT=`gcloud config list --format="value(core.project)"` # For Linux
        $ SET GOOGLE_CLOUD_PROJECT=`gcloud config list --format="value(core.project)"`     # For Windows
        $ mvn -DskipTests com.google.cloud.tools:jib-maven-plugin:build -Dimage=gcr.io/$GOOGLE_CLOUD_PROJECT/gs-springboot-gkeapp:v1
        $ docker run -ti --rm -p 8080:8080 gcr.io/$GOOGLE_CLOUD_PROJECT/gs-springboot-gkeapp:v1

Deploying Docker Image to Kubernetes Apps

11. Deploy your app to Kubernetes

         $ kubectl create deployment gs-springboot-gkeapp-dep --image=gcr.io/$GOOGLE_CLOUD_PROJECT/gs-springboot-gkeapp:v1
         $ kubectl create deployment gs-springboot-gkeapp-dep --image=gcr.io/$GOOGLE_CLOUD_PROJECT/gs-springboot-gkeapp:v1
         $ kubectl get pods
         $ kubectl get deployments

12. Create a service

         $ kubectl create service loadbalancer gs-springboot-gkeapp-glb --tcp=80:8080

13. Set New images for new version deployment

         $ kubectl set image deployment/gs-springboot-gkeapp gs-springboot-gkeapp=gcr.io/$GOOGLE_CLOUD_PROJECT/gs-springboot-gkeapp:v1

14. Delete images

         $ gcloud container images delete gcr.io/$GOOGLE_CLOUD_PROJECT/gke-springbootjavaapp:v2
         $ gcloud container images delete gcr.io/my-project-67279/gs-springboot-gkeapp:v1

15. Delete cluster

         $ gcloud container clusters delete myapp-cluster --zone us-central1-c
