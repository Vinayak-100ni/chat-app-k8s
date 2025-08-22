1. upload frontend and backend image on dockerhub
     -> docker build -t vinayak100ni/chat-app-k8s-backend:latest .
        docker push vinayak100ni/chat-app-k8s-backend:latest  (same for frontend also.)

2. create forntend , backend & mongodb service and deployment file.

3. port forwarding
   --> kubectl get svc -n app
       kubectl port-forward service/backend -n app 5001:5001 & (& for running in background).
     --- same for frontend (80)

4 now connect backend with database
