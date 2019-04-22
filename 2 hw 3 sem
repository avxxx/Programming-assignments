#include <sys/types.h>
#include <sys/stat.h>
#include <stdlib.h>
#include <dirent.h>
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <string.h>
#include <stdlib.h>
#include <errno.h>


//create new directory
void create_dir(char *dst_path)
{
        struct stat st;
        if(stat(dst_path, &st) == -1)
        {
                mkdir(dst_path, 0777);
        }
}

//archivation
void archivate(char *path)
{
            char command[1000];
            strcpy(command, "gzip ");
            strcat(command, path);

            system(command);

            printf("File %s archivated.\n", path);
}

//copying and archivating files from source dir to destination dir
void copy_files(char *source_path, char *dest_path)
{
        int source_fd, dest_fd;
        int error;
        unsigned char buffer[4096];

        //cheking copies
        struct stat info;
        struct stat inf;
        char s[256];
        strcpy(s, dest_path);
        strcat(s, ".gz");
        if ((stat(source_path, &info) != -1) && (stat(s, &inf) != -1))
        {
		if (inf.st_mtime > info.st_mtime)
                {
                        printf("%s already copied\n", source_path);
                        exit(1);
                }
        }

        //open files
        source_fd = open(source_path, O_RDONLY);
        if (source_fd == -1)
        {
                printf(" %s premission for reading denied\n", source_path);
                exit(1);
        }
        dest_fd = open(dest_path, O_RDWR | O_CREAT, 0777);

        //copying files
        int tmp;
        while(1)
        {
                error = read(source_fd, buffer, 4096);
                if (error == -1)
                {
                        printf("Cannot read file\n");
                        exit(1);
                }
                tmp = error;
                if (tmp == 0)
                {
                        break;
                }
                error = write(dest_fd, buffer, tmp);
                if (error == -1)
                {
                        printf("Error writing to file.\n");
                        exit(1);
                }
        }

        //closing files
        if(close(source_fd) == -1 || close(dest_fd) == -1 )
        {
                printf("Error closing files\n");
                return;//!
        }

        //archivating
        archivate(dest_path);

//operate source directory
void operate_dir(char *src_path, char *dst_path)
{
        DIR *dir = NULL;
        struct dirent *dent;
        struct stat fileInfo;

        dir = opendir(src_path);
        if(!dir)
        {
                perror("Cannot open directory");
                exit(1);
        };//!

        //recursion on all files in directory
        while ((dent = readdir(dir)) != NULL)
        {
                if(!((strcmp(dent->d_name,".")==0 || strcmp(dent->d_name,"..")==0 || (*dent->d_name) == '.' )))
                {
                        char source[1000];
                        strcpy(source, src_path);
                        strcat(source, "/");
                        strcat(source, dent-> d_name);//source = new path
                        char dest[1000];
                        strcpy(dest, dst_path);
                        strcat(dest, "/");
                        strcat(dest, dent-> d_name);// new dst_path of copying

                        if(stat(source, &fileInfo) >= 0)
                        {
                                if(S_ISREG(fileInfo.st_mode))
                                {
                                        //copy files from source to dest
                                        copy_files(source, dest);
                                }
                                if (S_ISDIR(fileInfo.st_mode))
                                {
                                        create_dir(dest);
                                        operate_dir(source, dest);
                                }
                        }
                }
        };
        closedir(dir);
}


int main(int argc, char* argv[])
{
        create_dir(argv[2]);
        operate_dir(argv[1], argv[2]);

        printf("Backup is succesful.\n");
        return 0;
}
