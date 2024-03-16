# PathPlannerAutoDashboard

A dashboard to easily view and select PathPlanner autos when using PathPlannerLib. In progress.

## Temporary example Java code
Run after initializing AutoBuilder

```java
private void publishAutoData() {
    StringBuilder jsonData = new StringBuilder();
    jsonData.append("{\"autoChoices\": [");
    for (String choice : AutoBuilder.getAllAutoNames()) {
        jsonData.append("{\"name\": \"").append(choice).append("\", \"poses\": [");
        List<Pose2d> poses = new ArrayList<>();

        for (PathPlannerPath path : PathPlannerAuto.getPathGroupFromAutoFile(choice)) {
            path.preventFlipping = true;
            poses.addAll(path.getPathPoses());
        }
        
        try {
            poses.set(0, PathPlannerAuto.getStaringPoseFromAutoFile(choice));
        } catch(RuntimeException e) {
            // Do nothing
        }
        
        for (Pose2d pose : poses) {
            jsonData
                .append("{\"x\": ")
                .append(pose.getTranslation().getX())
                .append(", \"y\": ")
                .append(pose.getTranslation().getY())
                .append(", \"rot\": ")
                .append(pose.getRotation().getRadians())
                .append("},");
        }
        jsonData.deleteCharAt(jsonData.length() - 1);
        jsonData.append("]},");
    }
    jsonData.deleteCharAt(jsonData.length() - 1);
    jsonData.append("]}");
    NetworkTableInstance.getDefault()
        .getTable("2826AutoDashboard")
        .getStringTopic("AutoData")
        .publish()
        .accept(jsonData.toString());
}
```