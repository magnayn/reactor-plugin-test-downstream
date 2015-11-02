
node() {
  
  // You could parse this out of a pom file, for example...
  def iNeedUpstreamVersion = "1.0";

  registerDownstream(iNeedUpstreamVersion);

  def upstreamRepo = calculateUpstream(currentBuild.rawBuild.causes);

  build();
}

def build() {
	println "Build build build";

	// Maybe even further downstream
	step([$class: 'FireEventStep', eventName: 'build', properties: """upstreamSha1=${env.GIT_COMMIT}
version=1.0"""]);
}

def registerDownstream(ver) {
  step([$class: 'RegisterReactorStep', scriptData: """
  
  println "REACTOR: Consider: " + event.jobName;
  println "event.eventProperties['version']";

  if( event.jobFullName.startsWith("Upstream/") && event.eventProperties['version'] == ${ver} )
    return true;

  return false;
   """ ]);
}

def calculateUpstream(causes) {
  def root = "http://jenkins/plugin/repository/project/";
  def cause = causes.find() { it instanceof com.nirima.reactor.ReactorCause };

  println "cause: ${cause}";

  if( cause != null && cause != false ) {
    return "${root}${cause.event.jobName}/Build/${cause.event.buildNumber}/repository/"
  } else {
    return "${root}Upstream/master/LastSuccessful/repository/"
  }  
}