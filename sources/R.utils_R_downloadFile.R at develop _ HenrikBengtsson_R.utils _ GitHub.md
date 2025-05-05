---
title: R.utils/R/downloadFile.R at develop · HenrikBengtsson/R.utils · GitHub
---

# R.utils/R/downloadFile.R at develop · HenrikBengtsson/R.utils · GitHub

url:: https://github.com/HenrikBengtsson/R.utils/blob/develop/R/downloadFile.R
up: [[sources]]

#source

1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

23

24

25

26

27

28

29

30

31

32

33

34

35

36

37

38

39

40

41

42

43

44

45

46

47

48

49

50

51

52

53

54

55

56

57

58

59

60

61

62

63

64

65

66

67

68

69

70

71

72

73

74

75

76

77

78

79

80

81

82

83

84

85

86

87

88

89

90

91

92

93

94

95

96

97

98

99

100

101

102

103

104

105

106

107

108

109

110

111

112

113

114

115

116

117

118

119

120

121

122

123

124

125

126

127

128

129

130

131

132

133

134

135

136

137

138

139

140

141

142

143

144

145

146

147

148

149

150

151

152

153

154

155

156

157

158

159

160

161

162

163

164

165

166

167

168

169

170

171

172

173

174

175

176

177

178

179

180

181

182

183

184

185

186

187

188

189

190

191

192

193

194

195

196

197

198

199

200

201

202

203

204

205

206

207

208

209

210

211

212

213

214

215

216

217

218

219

220

221

222

223

224

225

226

227

228

229

230

231

232

233

234

235

236

237

238

239

240

241

242

243

244

245

246

247

248

249

250

251

252

253

254

255

256

257

258

259

260

261

262

263

264

265

266

267

268

269

270

271

272

273  
###########################################################################/**  
# @RdocMethod downloadFile  
# @title "Downloads a file"  
# \item{url}{A @character string specifying the URL to be downloaded.}  
# \item{filename, path}{(optional) @character strings specifying the  
# local filename and the path of the downloaded file.}  
# \item{skip}{If @TRUE, an already downloaded file is skipped.}  
# \item{overwrite}{If @TRUE, an already downloaded file is overwritten,  
# otherwise an error is thrown.}  
# \item{...}{Additional arguments passed to @see "utils::download.file".}  
# \item{username, password}{@character strings specifying the username  
# and password for authenticated downloads. The alternative is to  
# specify these via the URL.}  
# \item{binary}{If @TRUE, the file is downloaded exactly "as is", that is,  
# byte by byte (recommended).}  
# \item{dropEmpty}{If @TRUE and the downloaded file is empty, the file  
# is ignored and @NULL is returned.}  
# \item{verbose}{A @logical, @integer, or a @see "Verbose" object.}  
# Returns the local pathname to the downloaded filename,  
# or @NULL if no file was downloaded.  
# Currently arguments \code{username} and \code{password} are only used  
# for downloads via URL protocol 'https'. The 'https' protocol requires  
# that either of 'curl' or 'wget' are available on the system.  
# pathname <- downloadFile("https://www.r-project.org/index.html", path="www.r-project.org/")  
# Internally @see "utils::download.file" is used.  
# That function may generate an empty file if the URL is not available.  
#*/###########################################################################  
setMethodS3( "downloadFile", "character", function( url, filename =basename( url), path = NULL, skip = TRUE, overwrite = ! skip, ..., username = NULL, password = NULL, binary = TRUE, dropEmpty = TRUE, verbose = FALSE) {  
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  
url <- Arguments $getCharacter( url)  
skip <- Arguments $getLogical( skip)  
overwrite <- Arguments $getLogical( overwrite)  
# Argument 'filename' & 'path':  
filename <- Arguments $getReadablePathname( filename, adjust = "url",  
pathname <- Arguments $getWritablePathname( filename, path = path,  
mustNotExist =( ! overwrite && ! skip))  
if ( !is.null( username)) {  
username <- Arguments $getCharacter( username)  
if ( !is.null( password)) {  
password <- Arguments $getCharacter( password)  
binary <- Arguments $getLogical( binary)  
verbose <- Arguments $getVerbose( verbose)  
on.exit(popState( verbose))  
verbose && enter( verbose, "Downloading URL")  
verbose && cat( verbose, "URL: ", url)  
protocol <- gsub( "^([^:]*)://.*", "\\1", url, ignore.case = TRUE)  
protocol <- tolower( protocol)  
verbose && cat( verbose, "Protocol: ", protocol)  
# Is username and password given by the URL?  
pattern <- "^([^:]*)://([^:]*):([^:]*)@.*"  
if (regexpr( pattern, url) != - 1) {  
if ( !is.null( username)) {  
warning( "Argument 'username' was overridden by username specified by argument 'url'.")  
if ( !is.null( password)) {  
warning( "Argument 'password' was overridden by password specified by argument 'url'.")  
username <- gsub( pattern, "\\2", url)  
password <- gsub( pattern, "\\3", url)  
verbose && cat( verbose, "Pathname: ", pathname)  
verbose && cat( verbose, "Already downloaded. Skipping.")  
verbose && exit( verbose)  
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  
# Download to a temporary pathname  
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  
pathnameT <- sprintf( "%s.tmp", pathname)  
pathnameT <- Arguments $getWritablePathname( pathnameT, mustNotExist = TRUE)  
if (isFile( pathnameT)) {  
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  
verbose && enter( verbose, "Downloading")  
if (is.element( protocol, c( "https"))) {  
verbose && enter( verbose, "Downloading via HTTPS")  
# Locate external executables  
bin <- Sys.which(c( "curl", "wget"))  
verbose && cat( verbose, "Available external executables:")  
verbose && print( verbose, bin)  
throw( "Cannot download file over HTTPS protocol. Failed to locate external download software (%s): %s", paste(sQuote(names( bin)), collapse = ", "), url)  
verbose && printf( verbose, "Using external download software %s: %s\n", sQuote(names( bin)), bin)  
verbose && enter( verbose, "Setting up command-line options")  
timeout <- getOption( "timeout")  
if ( !is.null( timeout)) {  
timeout <- as.integer( timeout)  
stopifnot(length( timeout) == 1L, !is.na( timeout))  
if (names( bin) == "curl") {  
# Less strict (=more likely to succeed)  
if ( !is.null( username)) {  
arg <- sprintf( "--user %s", username)  
if ( !is.null( password)) {  
arg <- sprintf( "%s:%s", arg, password)  
if ( !is.null( timeout)) {  
arg <- sprintf( "--connect-timeout %d", timeout)  
arg <- sprintf( "--output \"%s\"", pathnameT)  
} else if (names( bin) == "wget") {  
# Less strict (=more likely to succeed)  
arg <- "--no-check-certificate"  
if ( !is.null( username)) {  
arg <- sprintf( "--http-user=%s", username)  
if ( !is.null( password)) {  
arg <- sprintf( "--http-passwd=%s", password)  
if ( !is.null( timeout)) {  
arg <- sprintf( "--timeout=%d", timeout)  
arg <- sprintf( "--output-document=\"%s\"", pathnameT)  
verbose && print( verbose, args)  
verbose && exit( verbose)  
res <- system2( bin, args = args)  
verbose && exit( verbose)  
mode <- ifelse( binary, "wb", "w")  
verbose && cat( verbose, "Download mode: ", mode)  
res <- download.file( url, destfile = pathnameT, mode = mode,  
quiet = !isVisible( verbose), ...)  
verbose && cat( verbose, "Downloading finished\n")  
verbose && cat( verbose, "Download result:", res)  
verbose && exit( verbose)  
# Remove failed or "empty" downloads  
fi <- file.info2( pathnameT)  
verbose && cat( verbose, "Downloaded file:")  
verbose && str( verbose, fi)  
if ( res != 0 || is.na( fi $ size) || ( dropEmpty && fi $ size == 0)) {  
verbose && cat( verbose, "Removed downloaded file because download failed or the file was empty: ", pathnameT)  
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  
# Rename temporary pathname  
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -  
if ( !is.null( pathnameT)) {  
file.rename( pathnameT, pathname)  
if ( !isFile( pathname)) {  
throw( "Failed to rename temporary filename: ",  
pathnameT, " -> ", pathname)  
if (isFile( pathnameT)) {  
throw( "Failed to remove temporary filename: ", pathnameT)  
verbose && exit( verbose)
